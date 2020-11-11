---
title: Telemetrię HTTP REPL
author: scottaddie
description: Dowiedz się więcej na temat danych telemetrycznych zebranych przez REPL HTTP.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502030"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="e55e8-103">Telemetrię HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="e55e8-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="e55e8-104">[Pętla http Read-eval-Print (REPL)](xref:web-api/http-repl) zawiera funkcję telemetrii, która zbiera dane użycia.</span><span class="sxs-lookup"><span data-stu-id="e55e8-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="e55e8-105">Należy pamiętać, że zespół protokołu HTTP REPL rozumie, w jaki sposób narzędzie jest używane, aby można je było ulepszyć.</span><span class="sxs-lookup"><span data-stu-id="e55e8-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="e55e8-106">Jak zrezygnować</span><span class="sxs-lookup"><span data-stu-id="e55e8-106">How to opt out</span></span>

<span data-ttu-id="e55e8-107">Funkcja telemetrii HTTP REPL jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="e55e8-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="e55e8-108">Aby zrezygnować z funkcji telemetrii, należy ustawić `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` zmienną środowiskową na `1` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="e55e8-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="e55e8-109">Mogąc</span><span class="sxs-lookup"><span data-stu-id="e55e8-109">Disclosure</span></span>

<span data-ttu-id="e55e8-110">HttpRepl wyświetla tekst podobny do poniższego podczas pierwszego uruchomienia narzędzia.</span><span class="sxs-lookup"><span data-stu-id="e55e8-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="e55e8-111">Tekst może się nieco różnić w zależności od wersji uruchomionego narzędzia.</span><span class="sxs-lookup"><span data-stu-id="e55e8-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="e55e8-112">To "pierwsze uruchomienie" polega na tym, jak firma Microsoft powiadamia użytkownika o zbieraniu danych.</span><span class="sxs-lookup"><span data-stu-id="e55e8-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="e55e8-113">Punkty danych</span><span class="sxs-lookup"><span data-stu-id="e55e8-113">Data points</span></span>

<span data-ttu-id="e55e8-114">Funkcja telemetrii nie:</span><span class="sxs-lookup"><span data-stu-id="e55e8-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="e55e8-115">Zbieraj dane osobowe, takie jak nazwy użytkowników, adresy e-mail lub adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e55e8-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="e55e8-116">Skanuj żądania HTTP lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e55e8-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="e55e8-117">Dane są bezpiecznie przesyłane do serwerów firmy Microsoft i przechowywane w ograniczonym dostępie.</span><span class="sxs-lookup"><span data-stu-id="e55e8-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="e55e8-118">Ochrona prywatności jest dla nas ważna.</span><span class="sxs-lookup"><span data-stu-id="e55e8-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="e55e8-119">Jeśli podejrzewasz, że funkcja telemetrii zbiera poufne dane lub dane są w sposób niezabezpieczony lub niewłaściwie obsłużone, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="e55e8-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="e55e8-120">Zaplikowanie problemu w repozytorium [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="e55e8-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="e55e8-121">Wyślij wiadomość e-mail na adres [dotnet@microsoft.com](mailto:dotnet@microsoft.com) w celu zbadania problemu.</span><span class="sxs-lookup"><span data-stu-id="e55e8-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="e55e8-122">Funkcja telemetrii zbiera następujące dane.</span><span class="sxs-lookup"><span data-stu-id="e55e8-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="e55e8-123">Wersje zestawu SDK platformy .NET</span><span class="sxs-lookup"><span data-stu-id="e55e8-123">.NET SDK versions</span></span> | <span data-ttu-id="e55e8-124">Dane</span><span class="sxs-lookup"><span data-stu-id="e55e8-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="e55e8-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-125">>=5.0</span></span>        | <span data-ttu-id="e55e8-126">Sygnatura czasowa wywołania.</span><span class="sxs-lookup"><span data-stu-id="e55e8-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="e55e8-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-127">>=5.0</span></span>        | <span data-ttu-id="e55e8-128">Trzy-oktetowe adresy IP używane do określenia lokalizacji geograficznej.</span><span class="sxs-lookup"><span data-stu-id="e55e8-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="e55e8-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-129">>=5.0</span></span>        | <span data-ttu-id="e55e8-130">System operacyjny i wersja.</span><span class="sxs-lookup"><span data-stu-id="e55e8-130">Operating system and version.</span></span> |
| <span data-ttu-id="e55e8-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-131">>=5.0</span></span>        | <span data-ttu-id="e55e8-132">Identyfikator środowiska uruchomieniowego (RID), na którym jest uruchomione narzędzie.</span><span class="sxs-lookup"><span data-stu-id="e55e8-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="e55e8-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-133">>=5.0</span></span>        | <span data-ttu-id="e55e8-134">Czy narzędzie jest uruchomione w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="e55e8-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="e55e8-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-135">>=5.0</span></span>        | <span data-ttu-id="e55e8-136">Adres MAC w postaci skrótu Access Control: kryptograficzny (SHA256) i unikatowy identyfikator dla maszyny.</span><span class="sxs-lookup"><span data-stu-id="e55e8-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="e55e8-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-137">>=5.0</span></span>        | <span data-ttu-id="e55e8-138">Wersja jądra.</span><span class="sxs-lookup"><span data-stu-id="e55e8-138">Kernel version.</span></span> |
| <span data-ttu-id="e55e8-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-139">>=5.0</span></span>        | <span data-ttu-id="e55e8-140">Wersja HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="e55e8-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="e55e8-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-141">>=5.0</span></span>        | <span data-ttu-id="e55e8-142">Czy narzędzie zostało uruchomione z `help` `run` argumentami,, czy `connect` .</span><span class="sxs-lookup"><span data-stu-id="e55e8-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="e55e8-143">Rzeczywiste wartości argumentów nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="e55e8-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="e55e8-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-144">>=5.0</span></span>        | <span data-ttu-id="e55e8-145">Wywołano polecenie (na przykład `get` ) i czy zakończyło się ono pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="e55e8-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="e55e8-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-146">>=5.0</span></span>        | <span data-ttu-id="e55e8-147">Dla `connect` polecenia, czy `root` `base` argumenty, lub `openapi` zostały dostarczone.</span><span class="sxs-lookup"><span data-stu-id="e55e8-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="e55e8-148">Rzeczywiste wartości argumentów nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="e55e8-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="e55e8-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-149">>=5.0</span></span>        | <span data-ttu-id="e55e8-150">Dla `pref` polecenia — czy `get` `set` został wystawiony, a także do którego dostępu uzyskano dostęp.</span><span class="sxs-lookup"><span data-stu-id="e55e8-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="e55e8-151">Jeśli nie jest to dobrze znane preferencja, nazwa jest skrótem.</span><span class="sxs-lookup"><span data-stu-id="e55e8-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="e55e8-152">Wartość nie jest zbierana.</span><span class="sxs-lookup"><span data-stu-id="e55e8-152">The value isn't collected.</span></span> |
| <span data-ttu-id="e55e8-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-153">>=5.0</span></span>        | <span data-ttu-id="e55e8-154">Dla `set header` polecenia zostanie ustawiona nazwa nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e55e8-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="e55e8-155">Jeśli nie jest dobrze znanym nagłówkiem, nazwa jest skrótem.</span><span class="sxs-lookup"><span data-stu-id="e55e8-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="e55e8-156">Wartość nie jest zbierana.</span><span class="sxs-lookup"><span data-stu-id="e55e8-156">The value isn't collected.</span></span> |
| <span data-ttu-id="e55e8-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-157">>=5.0</span></span>        | <span data-ttu-id="e55e8-158">Dla polecenia, niezależnie od tego, czy `connect` użyto specjalnego przypadku `dotnet new webapi` , i czy został pominięty przez preferencję.</span><span class="sxs-lookup"><span data-stu-id="e55e8-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="e55e8-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e55e8-159">>=5.0</span></span>        | <span data-ttu-id="e55e8-160">Dla wszystkich poleceń HTTP (na przykład GET, POST, PUT), niezależnie od tego, czy każda z opcji została określona.</span><span class="sxs-lookup"><span data-stu-id="e55e8-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="e55e8-161">Wartości opcji nie są zbierane.</span><span class="sxs-lookup"><span data-stu-id="e55e8-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="e55e8-162">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e55e8-162">Additional resources</span></span>

* [<span data-ttu-id="e55e8-163">Dane telemetryczne zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="e55e8-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="e55e8-164">Interfejs wiersza polecenia platformy .NET Core dane telemetryczne</span><span class="sxs-lookup"><span data-stu-id="e55e8-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
