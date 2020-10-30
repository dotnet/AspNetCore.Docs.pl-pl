---
title: Przekaż pliki w ASP.NET Core
author: rick-anderson
description: Jak używać powiązania modelu i przesyłania strumieniowego do przekazywania plików w ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 14561bace565c104d0a9c926cad3105c4865e72a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061174"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="43693-103">Przekaż pliki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43693-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="43693-104">Przez [Steve Kowalski](https://ardalis.com/) i [Rutger burzy](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="43693-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="43693-105">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="43693-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43693-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="43693-107">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-107">Security considerations</span></span>

<span data-ttu-id="43693-108">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="43693-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="43693-109">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="43693-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="43693-110">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="43693-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="43693-111">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="43693-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="43693-112">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="43693-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="43693-113">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="43693-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="43693-114">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="43693-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="43693-115">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="43693-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="43693-116">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="43693-117">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="43693-118">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="43693-119">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="43693-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="43693-120">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( :::no-loc(Razor)::: automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="43693-120">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="43693-121">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="43693-122">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="43693-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="43693-123">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="43693-124">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="43693-125">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="43693-126">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="43693-127">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="43693-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-128">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="43693-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="43693-129">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="43693-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="43693-130">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="43693-131">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="43693-132">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="43693-133">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="43693-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="43693-134">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="43693-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="43693-135">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="43693-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="43693-136">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="43693-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="43693-137">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="43693-137">Storage scenarios</span></span>

<span data-ttu-id="43693-138">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="43693-138">Common storage options for files include:</span></span>

* <span data-ttu-id="43693-139">baza danych</span><span class="sxs-lookup"><span data-stu-id="43693-139">Database</span></span>

  * <span data-ttu-id="43693-140">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="43693-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="43693-141">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="43693-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="43693-142">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="43693-143">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="43693-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="43693-144">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="43693-144">For large file uploads:</span></span>
    * <span data-ttu-id="43693-145">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="43693-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="43693-146">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="43693-147">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="43693-148">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="43693-149">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="43693-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="43693-150">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="43693-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="43693-151">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="43693-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="43693-152">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="43693-153">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="43693-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="43693-154">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="43693-154">File upload scenarios</span></span>

<span data-ttu-id="43693-155">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="43693-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="43693-156">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="43693-156">**Buffering**</span></span>

<span data-ttu-id="43693-157">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="43693-158">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="43693-159">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="43693-160">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="43693-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="43693-161">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="43693-162">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="43693-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="43693-163">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="43693-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="43693-164">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="43693-164">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="43693-165">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="43693-165">**Streaming**</span></span>

<span data-ttu-id="43693-166">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="43693-167">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="43693-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="43693-168">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="43693-169">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="43693-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="43693-170">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="43693-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="43693-171">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="43693-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="43693-172">Poniższy przykład ilustruje użycie :::no-loc(Razor)::: formularza stron do przekazywania pojedynczego pliku ( *Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="43693-172">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="43693-173">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="43693-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="43693-174">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="43693-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="43693-175">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="43693-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="43693-176">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="43693-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="43693-177">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="43693-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="43693-178">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="43693-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="43693-179">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="43693-180">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="43693-181">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="43693-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="43693-182">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="43693-183">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="43693-184">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="43693-185">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="43693-186">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="43693-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="43693-187">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="43693-187">Applications should:</span></span>
>
> * <span data-ttu-id="43693-188">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="43693-189">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="43693-190">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="43693-191">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="43693-192">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="43693-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="43693-193">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-194">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-195">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-195">Validation</span></span>](#validation)

<span data-ttu-id="43693-196">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="43693-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="43693-197">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="43693-198">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="43693-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="43693-199">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="43693-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="43693-200">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="43693-200">Binding matches form files by name.</span></span> <span data-ttu-id="43693-201">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="43693-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="43693-202">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="43693-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="43693-203">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-203">The following example:</span></span>

* <span data-ttu-id="43693-204">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="43693-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="43693-205">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="43693-206">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="43693-207">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="43693-208">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="43693-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="43693-209">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="43693-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="43693-210">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="43693-211">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="43693-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="43693-212">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="43693-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="43693-213">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="43693-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="43693-214">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="43693-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="43693-215">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-216">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="43693-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="43693-217">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="43693-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="43693-218">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="43693-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="43693-219">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="43693-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="43693-220">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="43693-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="43693-221">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="43693-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="43693-222">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="43693-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="43693-223"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="43693-224">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="43693-225">`FileUpload`Jest używana w :::no-loc(Razor)::: formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="43693-225">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="43693-226">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="43693-227">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="43693-228">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="43693-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="43693-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="43693-230">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="43693-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="43693-231">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="43693-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="43693-232">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="43693-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="43693-233">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="43693-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="43693-234">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="43693-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="43693-235">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-236">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-237">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="43693-238">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="43693-238">Upload large files with streaming</span></span>

<span data-ttu-id="43693-239">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="43693-240">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="43693-241">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="43693-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="43693-242">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="43693-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="43693-243">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="43693-244">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w :::no-loc(cookie)::: (za pośrednictwem `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="43693-244">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="43693-245">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić :::no-loc(cookie)::: token żądania:</span><span class="sxs-lookup"><span data-stu-id="43693-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="43693-246">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="43693-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="43693-247">W przykładowej aplikacji `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ :::no-loc(Razor)::: Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="43693-247">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="43693-248">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="43693-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="43693-249">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="43693-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="43693-250">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="43693-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="43693-251">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="43693-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="43693-252">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="43693-253">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="43693-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="43693-254">`MultipartRequestHelper` ( *Narzędzia/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="43693-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="43693-255">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="43693-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="43693-256">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="43693-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="43693-257">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-257">Validation</span></span>

<span data-ttu-id="43693-258">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="43693-259">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="43693-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="43693-260">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-261">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="43693-262">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="43693-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="43693-263">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="43693-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="43693-264">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="43693-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="43693-265">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="43693-266">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="43693-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="43693-267">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="43693-267">Content validation</span></span>

<span data-ttu-id="43693-268">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="43693-269">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="43693-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="43693-270">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="43693-271">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="43693-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="43693-272">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="43693-273">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="43693-274">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="43693-275">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="43693-275">File extension validation</span></span>

<span data-ttu-id="43693-276">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="43693-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="43693-277">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="43693-278">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="43693-278">File signature validation</span></span>

<span data-ttu-id="43693-279">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="43693-280">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="43693-281">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="43693-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="43693-282">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="43693-283">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="43693-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="43693-284">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="43693-284">File name security</span></span>

<span data-ttu-id="43693-285">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="43693-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="43693-286">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="43693-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="43693-287">:::no-loc(Razor)::: automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="43693-287">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="43693-288">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="43693-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="43693-289">Poza :::no-loc(Razor)::: , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-289">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="43693-290">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="43693-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="43693-291">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="43693-292">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="43693-292">Size validation</span></span>

<span data-ttu-id="43693-293">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="43693-294">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="43693-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="43693-295">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="43693-296">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="43693-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="43693-297">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="43693-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="43693-298">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="43693-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="43693-299">W nie :::no-loc(Razor)::: formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-299">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="43693-300">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43693-300">In the following example:</span></span>

* <span data-ttu-id="43693-301">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="43693-302">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="43693-303">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="43693-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="43693-304">Dla :::no-loc(Razor)::: metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="43693-304">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="43693-305">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="43693-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="43693-306">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="43693-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="43693-307">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="43693-307">Multipart body length limit</span></span>

<span data-ttu-id="43693-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="43693-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="43693-309">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="43693-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="43693-310">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="43693-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="43693-311">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="43693-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="43693-313">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-313">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="43693-314">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-314">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="43693-315">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="43693-316">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-317">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="43693-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="43693-319">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-319">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="43693-320">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-320">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="43693-321">`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="43693-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="43693-322">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-322">Other Kestrel limits</span></span>

<span data-ttu-id="43693-323">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="43693-324">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="43693-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="43693-325">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="43693-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="43693-326">IIS</span><span class="sxs-lookup"><span data-stu-id="43693-326">IIS</span></span>

<span data-ttu-id="43693-327">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-328">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="43693-329">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="43693-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="43693-330">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="43693-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="43693-331">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="43693-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="43693-332">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="43693-332">Troubleshoot</span></span>

<span data-ttu-id="43693-333">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="43693-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="43693-334">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="43693-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="43693-335">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="43693-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="43693-336">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="43693-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="43693-337">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="43693-337">Connection failure</span></span>

<span data-ttu-id="43693-338">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="43693-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="43693-339">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="43693-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="43693-340">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="43693-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="43693-341">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="43693-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="43693-342">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="43693-343">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="43693-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="43693-344">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="43693-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="43693-345">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="43693-345">Stream was too long</span></span>

<span data-ttu-id="43693-346">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="43693-347">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="43693-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="43693-348">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="43693-349">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="43693-350">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43693-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="43693-351">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-351">Security considerations</span></span>

<span data-ttu-id="43693-352">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="43693-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="43693-353">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="43693-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="43693-354">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="43693-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="43693-355">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="43693-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="43693-356">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="43693-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="43693-357">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="43693-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="43693-358">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="43693-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="43693-359">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="43693-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="43693-360">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="43693-361">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="43693-362">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="43693-363">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="43693-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="43693-364">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( :::no-loc(Razor)::: automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="43693-364">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="43693-365">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="43693-366">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="43693-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="43693-367">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="43693-368">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="43693-369">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="43693-370">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="43693-371">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="43693-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-372">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="43693-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="43693-373">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="43693-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="43693-374">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="43693-375">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="43693-376">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="43693-377">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="43693-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="43693-378">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="43693-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="43693-379">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="43693-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="43693-380">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="43693-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="43693-381">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="43693-381">Storage scenarios</span></span>

<span data-ttu-id="43693-382">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="43693-382">Common storage options for files include:</span></span>

* <span data-ttu-id="43693-383">baza danych</span><span class="sxs-lookup"><span data-stu-id="43693-383">Database</span></span>

  * <span data-ttu-id="43693-384">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="43693-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="43693-385">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="43693-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="43693-386">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="43693-387">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="43693-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="43693-388">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="43693-388">For large file uploads:</span></span>
    * <span data-ttu-id="43693-389">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="43693-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="43693-390">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="43693-391">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="43693-392">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="43693-393">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="43693-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="43693-394">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="43693-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="43693-395">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="43693-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="43693-396">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="43693-397">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="43693-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="43693-398">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="43693-398">File upload scenarios</span></span>

<span data-ttu-id="43693-399">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="43693-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="43693-400">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="43693-400">**Buffering**</span></span>

<span data-ttu-id="43693-401">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="43693-402">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="43693-403">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="43693-404">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="43693-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="43693-405">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="43693-406">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="43693-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="43693-407">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="43693-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="43693-408">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="43693-408">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="43693-409">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="43693-409">**Streaming**</span></span>

<span data-ttu-id="43693-410">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="43693-411">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="43693-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="43693-412">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="43693-413">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="43693-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="43693-414">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="43693-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="43693-415">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="43693-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="43693-416">Poniższy przykład ilustruje użycie :::no-loc(Razor)::: formularza stron do przekazywania pojedynczego pliku ( *Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="43693-416">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="43693-417">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="43693-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="43693-418">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="43693-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="43693-419">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="43693-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="43693-420">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="43693-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="43693-421">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="43693-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="43693-422">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="43693-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="43693-423">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="43693-424">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="43693-425">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="43693-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="43693-426">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="43693-427">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="43693-428">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="43693-429">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="43693-430">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="43693-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="43693-431">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="43693-431">Applications should:</span></span>
>
> * <span data-ttu-id="43693-432">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="43693-433">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="43693-434">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="43693-435">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="43693-436">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="43693-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="43693-437">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-438">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-439">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-439">Validation</span></span>](#validation)

<span data-ttu-id="43693-440">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="43693-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="43693-441">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="43693-442">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="43693-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="43693-443">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="43693-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="43693-444">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="43693-444">Binding matches form files by name.</span></span> <span data-ttu-id="43693-445">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="43693-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="43693-446">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="43693-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="43693-447">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-447">The following example:</span></span>

* <span data-ttu-id="43693-448">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="43693-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="43693-449">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="43693-450">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="43693-451">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="43693-452">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="43693-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="43693-453">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="43693-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="43693-454">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="43693-455">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="43693-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="43693-456">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="43693-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="43693-457">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="43693-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="43693-458">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="43693-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="43693-459">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-460">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="43693-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="43693-461">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="43693-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="43693-462">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="43693-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="43693-463">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="43693-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="43693-464">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="43693-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="43693-465">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="43693-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="43693-466">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="43693-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="43693-467"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="43693-468">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="43693-469">`FileUpload`Jest używana w :::no-loc(Razor)::: formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="43693-469">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="43693-470">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="43693-471">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="43693-472">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="43693-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="43693-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="43693-474">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="43693-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="43693-475">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="43693-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="43693-476">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="43693-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="43693-477">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="43693-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="43693-478">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="43693-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="43693-479">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-480">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-481">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="43693-482">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="43693-482">Upload large files with streaming</span></span>

<span data-ttu-id="43693-483">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="43693-484">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="43693-485">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="43693-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="43693-486">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="43693-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="43693-487">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="43693-488">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w :::no-loc(cookie)::: (za pośrednictwem `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="43693-488">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="43693-489">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić :::no-loc(cookie)::: token żądania:</span><span class="sxs-lookup"><span data-stu-id="43693-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="43693-490">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="43693-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="43693-491">W przykładowej aplikacji `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ :::no-loc(Razor)::: Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="43693-491">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="43693-492">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="43693-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="43693-493">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="43693-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="43693-494">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="43693-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="43693-495">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="43693-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="43693-496">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="43693-497">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="43693-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="43693-498">`MultipartRequestHelper` ( *Narzędzia/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="43693-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="43693-499">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="43693-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="43693-500">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="43693-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="43693-501">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-501">Validation</span></span>

<span data-ttu-id="43693-502">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="43693-503">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="43693-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="43693-504">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-505">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="43693-506">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="43693-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="43693-507">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="43693-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="43693-508">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="43693-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="43693-509">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="43693-510">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="43693-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="43693-511">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="43693-511">Content validation</span></span>

<span data-ttu-id="43693-512">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="43693-513">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="43693-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="43693-514">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="43693-515">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="43693-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="43693-516">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="43693-517">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="43693-518">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="43693-519">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="43693-519">File extension validation</span></span>

<span data-ttu-id="43693-520">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="43693-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="43693-521">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="43693-522">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="43693-522">File signature validation</span></span>

<span data-ttu-id="43693-523">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="43693-524">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="43693-525">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="43693-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="43693-526">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="43693-527">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="43693-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="43693-528">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="43693-528">File name security</span></span>

<span data-ttu-id="43693-529">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="43693-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="43693-530">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="43693-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="43693-531">:::no-loc(Razor)::: automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="43693-531">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="43693-532">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="43693-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="43693-533">Poza :::no-loc(Razor)::: , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-533">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="43693-534">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="43693-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="43693-535">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="43693-536">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="43693-536">Size validation</span></span>

<span data-ttu-id="43693-537">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="43693-538">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="43693-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="43693-539">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="43693-540">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="43693-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="43693-541">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="43693-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="43693-542">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="43693-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="43693-543">W nie :::no-loc(Razor)::: formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-543">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="43693-544">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43693-544">In the following example:</span></span>

* <span data-ttu-id="43693-545">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="43693-546">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="43693-547">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="43693-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="43693-548">Dla :::no-loc(Razor)::: metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="43693-548">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="43693-549">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="43693-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="43693-550">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="43693-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="43693-551">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="43693-551">Multipart body length limit</span></span>

<span data-ttu-id="43693-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="43693-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="43693-553">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="43693-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="43693-554">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="43693-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="43693-555">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="43693-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="43693-557">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-557">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="43693-558">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-558">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="43693-559">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="43693-560">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-561">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="43693-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="43693-563">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-563">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="43693-564">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-564">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="43693-565">`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="43693-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="43693-566">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-566">Other Kestrel limits</span></span>

<span data-ttu-id="43693-567">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="43693-568">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="43693-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="43693-569">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="43693-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="43693-570">IIS</span><span class="sxs-lookup"><span data-stu-id="43693-570">IIS</span></span>

<span data-ttu-id="43693-571">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-572">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="43693-573">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="43693-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="43693-574">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="43693-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="43693-575">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="43693-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="43693-576">Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43693-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43693-577">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="43693-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="43693-578">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="43693-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="43693-579">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="43693-579">Troubleshoot</span></span>

<span data-ttu-id="43693-580">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="43693-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="43693-581">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="43693-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="43693-582">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="43693-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="43693-583">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="43693-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="43693-584">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="43693-584">Connection failure</span></span>

<span data-ttu-id="43693-585">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="43693-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="43693-586">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="43693-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="43693-587">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="43693-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="43693-588">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="43693-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="43693-589">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="43693-590">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="43693-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="43693-591">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="43693-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="43693-592">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="43693-592">Stream was too long</span></span>

<span data-ttu-id="43693-593">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="43693-594">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="43693-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="43693-595">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="43693-596">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="43693-597">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43693-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="43693-598">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-598">Security considerations</span></span>

<span data-ttu-id="43693-599">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="43693-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="43693-600">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="43693-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="43693-601">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="43693-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="43693-602">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="43693-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="43693-603">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="43693-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="43693-604">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="43693-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="43693-605">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="43693-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="43693-606">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="43693-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="43693-607">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="43693-608">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="43693-609">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="43693-610">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="43693-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="43693-611">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( :::no-loc(Razor)::: automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="43693-611">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="43693-612">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="43693-613">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="43693-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="43693-614">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="43693-615">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="43693-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="43693-616">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="43693-617">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="43693-618">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="43693-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-619">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="43693-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="43693-620">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="43693-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="43693-621">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="43693-622">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="43693-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="43693-623">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="43693-624">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="43693-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="43693-625">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="43693-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="43693-626">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="43693-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="43693-627">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="43693-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="43693-628">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="43693-628">Storage scenarios</span></span>

<span data-ttu-id="43693-629">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="43693-629">Common storage options for files include:</span></span>

* <span data-ttu-id="43693-630">baza danych</span><span class="sxs-lookup"><span data-stu-id="43693-630">Database</span></span>

  * <span data-ttu-id="43693-631">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="43693-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="43693-632">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="43693-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="43693-633">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="43693-634">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="43693-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="43693-635">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="43693-635">For large file uploads:</span></span>
    * <span data-ttu-id="43693-636">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="43693-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="43693-637">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="43693-638">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="43693-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="43693-639">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="43693-640">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="43693-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="43693-641">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="43693-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="43693-642">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="43693-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="43693-643">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="43693-644">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="43693-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="43693-645">Temat ilustruje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> może służyć do zapisywania <xref:System.IO.FileStream> do magazynu obiektów BLOB podczas pracy z <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="43693-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="43693-646">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="43693-646">File upload scenarios</span></span>

<span data-ttu-id="43693-647">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="43693-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="43693-648">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="43693-648">**Buffering**</span></span>

<span data-ttu-id="43693-649">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="43693-650">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="43693-651">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="43693-652">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="43693-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="43693-653">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="43693-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="43693-654">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="43693-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="43693-655">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="43693-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="43693-656">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="43693-656">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="43693-657">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="43693-657">**Streaming**</span></span>

<span data-ttu-id="43693-658">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="43693-659">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="43693-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="43693-660">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="43693-661">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="43693-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="43693-662">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="43693-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="43693-663">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="43693-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="43693-664">Poniższy przykład ilustruje użycie :::no-loc(Razor)::: formularza stron do przekazywania pojedynczego pliku ( *Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="43693-664">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="43693-665">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="43693-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="43693-666">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="43693-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="43693-667">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="43693-667">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="43693-668">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="43693-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="43693-669">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="43693-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="43693-670">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="43693-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="43693-671">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-671">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="43693-672">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="43693-673">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="43693-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="43693-674">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="43693-675">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="43693-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="43693-676">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="43693-677">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="43693-678">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="43693-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="43693-679">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="43693-679">Applications should:</span></span>
>
> * <span data-ttu-id="43693-680">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="43693-681">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="43693-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="43693-682">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="43693-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="43693-683">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="43693-684">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="43693-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="43693-685">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-686">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-687">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-687">Validation</span></span>](#validation)

<span data-ttu-id="43693-688">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="43693-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="43693-689">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="43693-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="43693-690">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="43693-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="43693-691">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="43693-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="43693-692">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="43693-692">Binding matches form files by name.</span></span> <span data-ttu-id="43693-693">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="43693-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="43693-694">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="43693-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="43693-695">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-695">The following example:</span></span>

* <span data-ttu-id="43693-696">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="43693-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="43693-697">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="43693-698">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="43693-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="43693-699">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-699">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="43693-700">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="43693-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="43693-701">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="43693-701">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="43693-702">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="43693-703">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="43693-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="43693-704">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="43693-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="43693-705">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="43693-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="43693-706">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="43693-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="43693-707">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-708">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="43693-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="43693-709">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="43693-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="43693-710">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="43693-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="43693-711">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="43693-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="43693-712">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="43693-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="43693-713">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="43693-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="43693-714">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="43693-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="43693-715"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="43693-716">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="43693-717">`FileUpload`Jest używana w :::no-loc(Razor)::: formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="43693-717">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="43693-718">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43693-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="43693-719">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-719">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="43693-720">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="43693-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="43693-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="43693-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="43693-722">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="43693-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="43693-723">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="43693-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="43693-724">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="43693-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="43693-725">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="43693-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="43693-726">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="43693-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="43693-727">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="43693-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="43693-728">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="43693-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="43693-729">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="43693-730">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="43693-730">Upload large files with streaming</span></span>

<span data-ttu-id="43693-731">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="43693-732">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="43693-733">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="43693-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="43693-734">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="43693-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="43693-735">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="43693-736">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w :::no-loc(cookie)::: (za pośrednictwem `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="43693-736">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="43693-737">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić :::no-loc(cookie)::: token żądania:</span><span class="sxs-lookup"><span data-stu-id="43693-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="43693-738">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="43693-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="43693-739">W przykładowej aplikacji `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ :::no-loc(Razor)::: Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="43693-739">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="43693-740">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="43693-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="43693-741">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="43693-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="43693-742">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="43693-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="43693-743">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="43693-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="43693-744">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="43693-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="43693-745">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="43693-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="43693-746">`MultipartRequestHelper` ( *Narzędzia/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="43693-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="43693-747">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="43693-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="43693-748">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="43693-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="43693-749">Walidacja</span><span class="sxs-lookup"><span data-stu-id="43693-749">Validation</span></span>

<span data-ttu-id="43693-750">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="43693-751">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="43693-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="43693-752">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="43693-753">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="43693-754">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="43693-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="43693-755">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="43693-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="43693-756">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="43693-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="43693-757">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="43693-758">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="43693-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="43693-759">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="43693-759">Content validation</span></span>

<span data-ttu-id="43693-760">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="43693-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="43693-761">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="43693-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="43693-762">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="43693-763">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="43693-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="43693-764">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="43693-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="43693-765">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="43693-766">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="43693-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="43693-767">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="43693-767">File extension validation</span></span>

<span data-ttu-id="43693-768">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="43693-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="43693-769">Przykład:</span><span class="sxs-lookup"><span data-stu-id="43693-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="43693-770">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="43693-770">File signature validation</span></span>

<span data-ttu-id="43693-771">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="43693-772">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="43693-773">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="43693-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="43693-774">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="43693-775">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="43693-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="43693-776">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="43693-776">File name security</span></span>

<span data-ttu-id="43693-777">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="43693-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="43693-778">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="43693-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="43693-779">:::no-loc(Razor)::: automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="43693-779">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="43693-780">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="43693-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="43693-781">Poza :::no-loc(Razor)::: , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43693-781">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="43693-782">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="43693-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="43693-783">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43693-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="43693-784">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="43693-784">Size validation</span></span>

<span data-ttu-id="43693-785">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="43693-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="43693-786">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="43693-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="43693-787">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="43693-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="43693-788">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="43693-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="43693-789">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="43693-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="43693-790">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="43693-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="43693-791">W nie :::no-loc(Razor)::: formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="43693-791">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="43693-792">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43693-792">In the following example:</span></span>

* <span data-ttu-id="43693-793">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="43693-794">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="43693-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="43693-795">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="43693-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="43693-796">Dla :::no-loc(Razor)::: metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="43693-796">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="43693-797">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="43693-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="43693-798">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="43693-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="43693-799">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="43693-799">Multipart body length limit</span></span>

<span data-ttu-id="43693-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="43693-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="43693-801">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="43693-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="43693-802">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="43693-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="43693-803">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="43693-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="43693-805">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-805">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="43693-806">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-806">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="43693-807">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="43693-808">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-809">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="43693-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="43693-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="43693-811">W :::no-loc(Razor)::: aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="43693-811">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="43693-812">W :::no-loc(Razor)::: aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="43693-812">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="43693-813">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="43693-813">Other Kestrel limits</span></span>

<span data-ttu-id="43693-814">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="43693-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="43693-815">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="43693-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="43693-816">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="43693-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="43693-817">IIS</span><span class="sxs-lookup"><span data-stu-id="43693-817">IIS</span></span>

<span data-ttu-id="43693-818">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="43693-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="43693-819">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="43693-820">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="43693-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="43693-821">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="43693-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="43693-822">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="43693-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="43693-823">Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43693-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43693-824">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="43693-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="43693-825">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="43693-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="43693-826">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="43693-826">Troubleshoot</span></span>

<span data-ttu-id="43693-827">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="43693-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="43693-828">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="43693-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="43693-829">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="43693-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="43693-830">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="43693-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="43693-831">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="43693-831">Connection failure</span></span>

<span data-ttu-id="43693-832">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="43693-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="43693-833">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="43693-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="43693-834">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="43693-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="43693-835">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="43693-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="43693-836">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="43693-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="43693-837">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="43693-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="43693-838">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="43693-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="43693-839">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="43693-839">Stream was too long</span></span>

<span data-ttu-id="43693-840">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="43693-841">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="43693-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="43693-842">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="43693-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="43693-843">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="43693-843">Additional resources</span></span>

* [<span data-ttu-id="43693-844">Opróżnianie żądań połączenia HTTP</span><span class="sxs-lookup"><span data-stu-id="43693-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="43693-845">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="43693-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="43693-846">Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Środki zaradcze</span><span class="sxs-lookup"><span data-stu-id="43693-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="43693-847">Wzorce projektowe chmury platformy Azure: wzorzec klucza portiera</span><span class="sxs-lookup"><span data-stu-id="43693-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
