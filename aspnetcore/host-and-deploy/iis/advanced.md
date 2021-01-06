---
title: Konfiguracja zaawansowana
author: rick-anderson
description: Zaawansowana konfiguracja przy użyciu modułu ASP.NET Core i Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058619"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="d8c7d-103">Zaawansowana konfiguracja modułu ASP.NET Core i usług IIS</span><span class="sxs-lookup"><span data-stu-id="d8c7d-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="d8c7d-104">W tym artykule omówiono zaawansowane opcje konfiguracji i scenariusze dotyczące modułu ASP.NET Core i usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="d8c7d-105">Modyfikowanie rozmiaru stosu</span><span class="sxs-lookup"><span data-stu-id="d8c7d-105">Modify the stack size</span></span>

<span data-ttu-id="d8c7d-106">*Stosuje się tylko w przypadku korzystania z modelu hostingu w procesie.*</span><span class="sxs-lookup"><span data-stu-id="d8c7d-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="d8c7d-107">Skonfiguruj zarządzany rozmiar stosu przy użyciu `stackSize` Ustawienia w bajtach w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="d8c7d-108">Domyślny rozmiar to 1 048 576 bajtów (1 MB).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="d8c7d-109">Poniższy przykład zmienia rozmiar stosu na 2 MB (2 097 152 bajtów):</span><span class="sxs-lookup"><span data-stu-id="d8c7d-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="d8c7d-110">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="d8c7d-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="d8c7d-111">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="d8c7d-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="d8c7d-112">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="d8c7d-113">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="d8c7d-114">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="d8c7d-115">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="d8c7d-116">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="d8c7d-117">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="d8c7d-118">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="d8c7d-119">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="d8c7d-120">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="d8c7d-121">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="d8c7d-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="d8c7d-122">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami `TrustedInstaller` konta.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="d8c7d-123">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w `applicationHost.config` pliku w udziale.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="d8c7d-124">W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :</span><span class="sxs-lookup"><span data-stu-id="d8c7d-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="d8c7d-125">Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="d8c7d-126">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="d8c7d-127">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-127">Run the installer.</span></span>
1. <span data-ttu-id="d8c7d-128">Wyeksportuj zaktualizowany `applicationHost.config` plik do udziału plików.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="d8c7d-129">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="d8c7d-130">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="d8c7d-130">Data protection</span></span>

<span data-ttu-id="d8c7d-131">[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="d8c7d-132">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d8c7d-133">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d8c7d-134">Jeśli pierścień klucza ochrony danych jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d8c7d-135">cookieTokeny uwierzytelniania na podstawie wszystkich są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="d8c7d-136">Użytkownicy muszą ponownie zalogować się przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="d8c7d-137">Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d8c7d-138">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d8c7d-139">Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="d8c7d-140">**Tworzenie kluczy rejestru ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="d8c7d-141">Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="d8c7d-142">Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="d8c7d-143">W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt ochrony danych Provision-AutoGenKeys.ps1 programu PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="d8c7d-144">Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="d8c7d-145">Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="d8c7d-146">W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="d8c7d-147">Domyślnie klucze nie są szyfrowane.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="d8c7d-148">Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym działa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="d8c7d-149">Certyfikat x509 może służyć do ochrony kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="d8c7d-150">Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="d8c7d-151">Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="d8c7d-152">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="d8c7d-153">**Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="d8c7d-154">To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="d8c7d-155">Ustaw **Załaduj profil użytkownika** na `True` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="d8c7d-156">Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="d8c7d-157">Klucze są utrwalane w `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folderze.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="d8c7d-158">Należy również włączyć [ `setProfileEnvironment` atrybut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="d8c7d-159">Wartość domyślna `setProfileEnvironment` to `true` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="d8c7d-160">W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="d8c7d-161">Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="d8c7d-162">Przejdź do folderu `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="d8c7d-163">Otwórz plik `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="d8c7d-164">Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="d8c7d-165">Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="d8c7d-166">**Używanie systemu plików jako magazynu kluczy**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="d8c7d-167">Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="d8c7d-168">Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="d8c7d-169">Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="d8c7d-170">W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="d8c7d-171">Użyj udziału plików, do którego mają dostęp wszystkie maszyny.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="d8c7d-172">Wdróż certyfikat x509 na każdym komputerze.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="d8c7d-173">Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="d8c7d-174">**Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="d8c7d-175">System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="d8c7d-176">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="d8c7d-177">Konfiguracja usług IIS</span><span class="sxs-lookup"><span data-stu-id="d8c7d-177">IIS configuration</span></span>

<span data-ttu-id="d8c7d-178">**Systemy operacyjne Windows Server**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="d8c7d-179">Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="d8c7d-180">Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="d8c7d-181">W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="d8c7d-183">Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="d8c7d-184">Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="d8c7d-186">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d8c7d-187">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="d8c7d-188">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d8c7d-189">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie `<windowsAuthentication>` systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d8c7d-190">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d8c7d-191">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d8c7d-192">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="d8c7d-193">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d8c7d-194">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d8c7d-195">Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="d8c7d-196">Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="d8c7d-197">**Systemy operacyjne Windows dla komputerów stacjonarnych**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="d8c7d-198">Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d8c7d-199">Przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**  >  **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="d8c7d-200">Otwórz węzeł **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="d8c7d-201">Otwórz węzeł **Narzędzia do zarządzania siecią Web** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="d8c7d-202">Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="d8c7d-203">Zaznacz pole wyboru **usług World Wide Web Services**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d8c7d-204">Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="d8c7d-205">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d8c7d-206">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="d8c7d-207">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d8c7d-208">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie `<windowsAuthentication>` systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d8c7d-209">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d8c7d-210">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d8c7d-211">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d8c7d-212">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d8c7d-213">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d8c7d-214">Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-214">If the IIS installation requires a restart, restart the system.</span></span>

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="d8c7d-216">Katalogi wirtualne</span><span class="sxs-lookup"><span data-stu-id="d8c7d-216">Virtual Directories</span></span>

<span data-ttu-id="d8c7d-217">[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="d8c7d-218">Aplikacja może być hostowana jako [podaplikacja](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="d8c7d-219">Aplikacje podrzędne</span><span class="sxs-lookup"><span data-stu-id="d8c7d-219">Sub-applications</span></span>

<span data-ttu-id="d8c7d-220">Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="d8c7d-221">Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="d8c7d-222">Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="d8c7d-223">Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="d8c7d-224">W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="d8c7d-225">Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="d8c7d-226">Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="d8c7d-227">Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="d8c7d-228">Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="d8c7d-229">Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="d8c7d-230">Ustanów pulę aplikacji dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="d8c7d-231">Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="d8c7d-232">Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="d8c7d-233">Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="d8c7d-234">W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="d8c7d-235">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-235">Select **OK**.</span></span>

<span data-ttu-id="d8c7d-236">Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="d8c7d-237">Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="d8c7d-238">Pule aplikacji</span><span class="sxs-lookup"><span data-stu-id="d8c7d-238">Application Pools</span></span>

<span data-ttu-id="d8c7d-239">Izolacja puli aplikacji jest określana przez model hostingu:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="d8c7d-240">Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="d8c7d-241">Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="d8c7d-242">Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="d8c7d-243">Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="d8c7d-244">Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="d8c7d-245">Pula aplikacji Identity</span><span class="sxs-lookup"><span data-stu-id="d8c7d-245">Application Pool Identity</span></span>

<span data-ttu-id="d8c7d-246">Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="d8c7d-247">W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="d8c7d-248">W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj `ApplicationPoolIdentity` :</span><span class="sxs-lookup"><span data-stu-id="d8c7d-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

<span data-ttu-id="d8c7d-250">Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="d8c7d-251">Zasoby mogą być zabezpieczone przy użyciu tej tożsamości.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="d8c7d-252">Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="d8c7d-253">Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="d8c7d-254">Otwórz Eksploratora Windows i przejdź do katalogu.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="d8c7d-255">Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="d8c7d-256">Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="d8c7d-257">Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="d8c7d-258">Wprowadź `IIS AppPool\{APP POOL NAME}` Format, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji, w polu **Wprowadź nazwy obiektów do wybrania** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="d8c7d-259">Wybierz przycisk **Sprawdź nazwy** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-259">Select the **Check Names** button.</span></span> <span data-ttu-id="d8c7d-260">W polu *Domyślna pula aplikacji* Sprawdź nazwy używane przez program `IIS AppPool\DefaultAppPool` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="d8c7d-261">Po wybraniu przycisku **Sprawdź nazwy** `DefaultAppPool` w obszarze nazwy obiektów jest wskazywana wartość.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="d8c7d-262">Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="d8c7d-263">Użyj `IIS AppPool\{APP POOL NAME}` formatu, gdzie symbol zastępczy `{APP POOL NAME}` jest nazwą puli aplikacji, podczas sprawdzania nazwy obiektu.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="d8c7d-265">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-265">Select **OK**.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="d8c7d-267">&amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="d8c7d-268">Podaj dodatkowe uprawnienia zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="d8c7d-269">Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="d8c7d-270">Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="d8c7d-271">Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d8c7d-272">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d8c7d-272">HTTP/2 support</span></span>

<span data-ttu-id="d8c7d-273">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d8c7d-274">W procesie</span><span class="sxs-lookup"><span data-stu-id="d8c7d-274">In-process</span></span>
  * <span data-ttu-id="d8c7d-275">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="d8c7d-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d8c7d-276">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d8c7d-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d8c7d-277">Pozaprocesowe</span><span class="sxs-lookup"><span data-stu-id="d8c7d-277">Out-of-process</span></span>
  * <span data-ttu-id="d8c7d-278">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="d8c7d-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d8c7d-279">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d8c7d-280">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d8c7d-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d8c7d-281">W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d8c7d-282">W przypadku wdrożenia poza procesem w przypadku ustanowienia połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d8c7d-283">Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d8c7d-284">Protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d8c7d-285">Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d8c7d-286">Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="d8c7d-287">Żądania inspekcji wstępnej CORS</span><span class="sxs-lookup"><span data-stu-id="d8c7d-287">CORS preflight requests</span></span>

<span data-ttu-id="d8c7d-288">*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="d8c7d-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="d8c7d-289">W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="d8c7d-290">Aby dowiedzieć się, jak skonfigurować obsługę usług IIS w programie w `web.config` celu przekazywania żądań opcji, zobacz [Włączanie żądań między źródłami w programie ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="d8c7d-291">Moduł inicjalizacji aplikacji i limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="d8c7d-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="d8c7d-292">Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="d8c7d-293">[Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](xref:host-and-deploy/iis/in-process-hosting) lub [poza procesem](xref:host-and-deploy/iis/out-of-process-hosting) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="d8c7d-294">[Limit czasu bezczynności](#idle-timeout): w trakcie okresu braku aktywności aplikacja hostowana [w procesie](xref:host-and-deploy/iis/in-process-hosting) może zostać skonfigurowana w taki sposób, aby nie przekroczyć limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="d8c7d-295">Moduł inicjalizacji aplikacji</span><span class="sxs-lookup"><span data-stu-id="d8c7d-295">Application Initialization Module</span></span>

<span data-ttu-id="d8c7d-296">*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*</span><span class="sxs-lookup"><span data-stu-id="d8c7d-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="d8c7d-297">[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="d8c7d-298">Żądanie wyzwala uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-298">The request triggers the app to start.</span></span> <span data-ttu-id="d8c7d-299">Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="d8c7d-300">Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="d8c7d-301">Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="d8c7d-302">Przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**  >  **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d8c7d-303">Otwórz   >    >  **funkcje projektowania aplikacji** Internet Information Services World Wide Web Services.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="d8c7d-304">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d8c7d-305">W systemie Windows Server 2008 R2 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="d8c7d-306">Otwórz **Kreatora dodawania ról i funkcji**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="d8c7d-307">W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="d8c7d-308">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d8c7d-309">Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="d8c7d-310">Za pomocą Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="d8c7d-311">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d8c7d-312">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d8c7d-313">Domyślny **tryb uruchamiania** to `OnDemand` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="d8c7d-314">Ustaw **tryb uruchamiania** na `AlwaysRunning` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="d8c7d-315">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-315">Select **OK**.</span></span>
  1. <span data-ttu-id="d8c7d-316">Otwórz węzeł **Lokacje** w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d8c7d-317">Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web**  >  .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d8c7d-318">Ustawienie domyślnego **wstępnego ładowania jest włączone** `False` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="d8c7d-319">Ustaw **funkcję wstępnego ładowania** na `True` .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="d8c7d-320">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-320">Select **OK**.</span></span>

* <span data-ttu-id="d8c7d-321">Przy użyciu `web.config` , Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w pliku aplikacji `web.config` :</span><span class="sxs-lookup"><span data-stu-id="d8c7d-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="d8c7d-322">Limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="d8c7d-322">Idle Timeout</span></span>

<span data-ttu-id="d8c7d-323">*Dotyczy tylko aplikacji hostowanych w procesie.*</span><span class="sxs-lookup"><span data-stu-id="d8c7d-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="d8c7d-324">Aby zapobiec przekroczeniu przez aplikację, należy ustawić limit czasu bezczynności puli aplikacji przy użyciu Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="d8c7d-325">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d8c7d-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="d8c7d-326">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="d8c7d-327">Domyślny **limit czasu bezczynności (w minutach)** to `20` minuty.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="d8c7d-328">Ustaw **limit czasu bezczynności (w minutach)** na `0` (zero).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="d8c7d-329">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-329">Select **OK**.</span></span>
1. <span data-ttu-id="d8c7d-330">Odtwórz proces roboczy.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-330">Recycle the worker process.</span></span>

<span data-ttu-id="d8c7d-331">Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](xref:host-and-deploy/iis/out-of-process-hosting) aplikacji, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="d8c7d-332">Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="d8c7d-333">Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="d8c7d-334">Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="d8c7d-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="d8c7d-335">Inicjowanie aplikacji usług IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="d8c7d-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="d8c7d-336">[Inicjowanie `<applicationInitialization>` aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="d8c7d-337">[Ustawienia modelu procesów dla `<processModel>` puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="d8c7d-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="d8c7d-338">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="d8c7d-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="d8c7d-339">Moduł</span><span class="sxs-lookup"><span data-stu-id="d8c7d-339">Module</span></span>

<span data-ttu-id="d8c7d-340">**IIS (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="d8c7d-341">**IIS Express (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="d8c7d-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="d8c7d-342">Schemat</span><span class="sxs-lookup"><span data-stu-id="d8c7d-342">Schema</span></span>

<span data-ttu-id="d8c7d-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="d8c7d-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="d8c7d-345">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="d8c7d-345">Configuration</span></span>

<span data-ttu-id="d8c7d-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="d8c7d-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d8c7d-347">**IIS Express**</span></span>

* <span data-ttu-id="d8c7d-348">Program Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="d8c7d-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="d8c7d-349">*iisexpress.exe* Interfejs `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="d8c7d-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="d8c7d-350">Pliki można znaleźć, wyszukując je `aspnetcore` w `applicationHost.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="d8c7d-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
