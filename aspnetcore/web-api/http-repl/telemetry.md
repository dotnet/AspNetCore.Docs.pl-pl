---
title: Telemetrię HttpRepl
author: scottaddie
description: Dowiedz się więcej o telemetrii zebranej przez HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550611"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="0183f-103">Telemetrię HttpRepl</span><span class="sxs-lookup"><span data-stu-id="0183f-103">HttpRepl telemetry</span></span>

<span data-ttu-id="0183f-104">[HttpRepl](xref:web-api/http-repl) zawiera funkcję telemetrii, która zbiera dane dotyczące użycia.</span><span class="sxs-lookup"><span data-stu-id="0183f-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="0183f-105">Należy pamiętać, że zespół HttpRepl rozumie, w jaki sposób narzędzie jest używane, aby można je było ulepszyć.</span><span class="sxs-lookup"><span data-stu-id="0183f-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="0183f-106">Jak zrezygnować</span><span class="sxs-lookup"><span data-stu-id="0183f-106">How to opt out</span></span>

<span data-ttu-id="0183f-107">Funkcja telemetrii HttpRepl jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="0183f-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="0183f-108">Aby zrezygnować z funkcji telemetrii, należy ustawić `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` zmienną środowiskową na `1` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="0183f-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="0183f-109">Mogąc</span><span class="sxs-lookup"><span data-stu-id="0183f-109">Disclosure</span></span>

<span data-ttu-id="0183f-110">Podczas pierwszego uruchomienia narzędzia HttpRepl wyświetla tekst podobny do poniższego.</span><span class="sxs-lookup"><span data-stu-id="0183f-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="0183f-111">Tekst może się nieco różnić w zależności od wersji uruchomionego narzędzia.</span><span class="sxs-lookup"><span data-stu-id="0183f-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="0183f-112">To "pierwsze uruchomienie" polega na tym, jak firma Microsoft powiadamia użytkownika o zbieraniu danych.</span><span class="sxs-lookup"><span data-stu-id="0183f-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="0183f-113">Aby pominąć tekst środowiska "pierwsze uruchomienie", należy ustawić `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` zmienną środowiskową na `1` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="0183f-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="0183f-114">Punkty danych</span><span class="sxs-lookup"><span data-stu-id="0183f-114">Data points</span></span>

<span data-ttu-id="0183f-115">Funkcja telemetrii nie:</span><span class="sxs-lookup"><span data-stu-id="0183f-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="0183f-116">Zbieraj dane osobowe, takie jak nazwy użytkowników, adresy e-mail lub adresy URL.</span><span class="sxs-lookup"><span data-stu-id="0183f-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="0183f-117">Skanuj żądania HTTP lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0183f-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="0183f-118">Dane są bezpiecznie przesyłane do serwerów firmy Microsoft i przechowywane w ograniczonym dostępie.</span><span class="sxs-lookup"><span data-stu-id="0183f-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="0183f-119">Ochrona prywatności jest dla nas ważna.</span><span class="sxs-lookup"><span data-stu-id="0183f-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="0183f-120">Jeśli podejrzewasz, że funkcja telemetrii zbiera poufne dane lub dane są w sposób niezabezpieczony lub niewłaściwie obsłużone, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="0183f-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="0183f-121">Zaplikowanie problemu w repozytorium [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="0183f-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="0183f-122">Wyślij wiadomość e-mail na adres [dotnet@microsoft.com](mailto:dotnet@microsoft.com) w celu zbadania problemu.</span><span class="sxs-lookup"><span data-stu-id="0183f-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="0183f-123">Funkcja telemetrii zbiera następujące dane.</span><span class="sxs-lookup"><span data-stu-id="0183f-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="0183f-124">Wersje zestawu SDK platformy .NET</span><span class="sxs-lookup"><span data-stu-id="0183f-124">.NET SDK versions</span></span> | <span data-ttu-id="0183f-125">Dane</span><span class="sxs-lookup"><span data-stu-id="0183f-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="0183f-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-126">>=5.0</span></span>        | <span data-ttu-id="0183f-127">Sygnatura czasowa wywołania.</span><span class="sxs-lookup"><span data-stu-id="0183f-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="0183f-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-128">>=5.0</span></span>        | <span data-ttu-id="0183f-129">Trzy-oktetowe adresy IP używane do określenia lokalizacji geograficznej.</span><span class="sxs-lookup"><span data-stu-id="0183f-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="0183f-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-130">>=5.0</span></span>        | <span data-ttu-id="0183f-131">System operacyjny i wersja.</span><span class="sxs-lookup"><span data-stu-id="0183f-131">Operating system and version.</span></span> |
| <span data-ttu-id="0183f-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-132">>=5.0</span></span>        | <span data-ttu-id="0183f-133">Identyfikator środowiska uruchomieniowego (RID), na którym jest uruchomione narzędzie.</span><span class="sxs-lookup"><span data-stu-id="0183f-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="0183f-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-134">>=5.0</span></span>        | <span data-ttu-id="0183f-135">Czy narzędzie jest uruchomione w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="0183f-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="0183f-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-136">>=5.0</span></span>        | <span data-ttu-id="0183f-137">Adres MAC w postaci skrótu Access Control: kryptograficzny (SHA256) i unikatowy identyfikator dla maszyny.</span><span class="sxs-lookup"><span data-stu-id="0183f-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="0183f-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-138">>=5.0</span></span>        | <span data-ttu-id="0183f-139">Wersja jądra.</span><span class="sxs-lookup"><span data-stu-id="0183f-139">Kernel version.</span></span> |
| <span data-ttu-id="0183f-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-140">>=5.0</span></span>        | <span data-ttu-id="0183f-141">Wersja HttpRepl.</span><span class="sxs-lookup"><span data-stu-id="0183f-141">HttpRepl version.</span></span> |
| <span data-ttu-id="0183f-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-142">>=5.0</span></span>        | <span data-ttu-id="0183f-143">Czy narzędzie zostało uruchomione z `help` `run` argumentami,, czy `connect` .</span><span class="sxs-lookup"><span data-stu-id="0183f-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="0183f-144">Rzeczywiste wartości argumentów nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="0183f-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="0183f-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-145">>=5.0</span></span>        | <span data-ttu-id="0183f-146">Wywołano polecenie (na przykład `get` ) i czy zakończyło się ono pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0183f-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="0183f-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-147">>=5.0</span></span>        | <span data-ttu-id="0183f-148">Dla `connect` polecenia, czy `root` `base` argumenty, lub `openapi` zostały dostarczone.</span><span class="sxs-lookup"><span data-stu-id="0183f-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="0183f-149">Rzeczywiste wartości argumentów nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="0183f-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="0183f-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-150">>=5.0</span></span>        | <span data-ttu-id="0183f-151">Dla `pref` polecenia — czy `get` `set` został wystawiony, a także do którego dostępu uzyskano dostęp.</span><span class="sxs-lookup"><span data-stu-id="0183f-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="0183f-152">Jeśli nie jest to dobrze znane preferencja, nazwa jest skrótem.</span><span class="sxs-lookup"><span data-stu-id="0183f-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="0183f-153">Wartość nie jest zbierana.</span><span class="sxs-lookup"><span data-stu-id="0183f-153">The value isn't collected.</span></span> |
| <span data-ttu-id="0183f-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-154">>=5.0</span></span>        | <span data-ttu-id="0183f-155">Dla `set header` polecenia zostanie ustawiona nazwa nagłówka.</span><span class="sxs-lookup"><span data-stu-id="0183f-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="0183f-156">Jeśli nie jest dobrze znanym nagłówkiem, nazwa jest skrótem.</span><span class="sxs-lookup"><span data-stu-id="0183f-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="0183f-157">Wartość nie jest zbierana.</span><span class="sxs-lookup"><span data-stu-id="0183f-157">The value isn't collected.</span></span> |
| <span data-ttu-id="0183f-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-158">>=5.0</span></span>        | <span data-ttu-id="0183f-159">Dla polecenia, niezależnie od tego, czy `connect` użyto specjalnego przypadku `dotnet new webapi` , i czy został pominięty przez preferencję.</span><span class="sxs-lookup"><span data-stu-id="0183f-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="0183f-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0183f-160">>=5.0</span></span>        | <span data-ttu-id="0183f-161">Dla wszystkich poleceń HTTP (na przykład GET, POST, PUT), niezależnie od tego, czy każda z opcji została określona.</span><span class="sxs-lookup"><span data-stu-id="0183f-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="0183f-162">Wartości opcji nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="0183f-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0183f-163">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0183f-163">Additional resources</span></span>

* [<span data-ttu-id="0183f-164">Dane telemetryczne zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="0183f-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="0183f-165">Interfejs wiersza polecenia platformy .NET Core dane telemetryczne</span><span class="sxs-lookup"><span data-stu-id="0183f-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
