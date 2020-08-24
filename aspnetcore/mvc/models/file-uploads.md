---
title: Przekaż pliki w ASP.NET Core
author: rick-anderson
description: Jak używać powiązania modelu i przesyłania strumieniowego do przekazywania plików w ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720282"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="13f2d-103">Przekaż pliki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13f2d-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="13f2d-104">Przez [Steve Kowalski](https://ardalis.com/) i [Rutger burzy](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="13f2d-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="13f2d-105">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="13f2d-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13f2d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="13f2d-107">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-107">Security considerations</span></span>

<span data-ttu-id="13f2d-108">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="13f2d-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="13f2d-109">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="13f2d-110">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="13f2d-111">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="13f2d-112">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="13f2d-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="13f2d-113">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="13f2d-114">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="13f2d-115">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="13f2d-116">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="13f2d-117">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="13f2d-118">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="13f2d-119">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="13f2d-120">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="13f2d-121">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="13f2d-122">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="13f2d-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="13f2d-123">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="13f2d-124">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="13f2d-125">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="13f2d-126">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="13f2d-127">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="13f2d-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-128">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="13f2d-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="13f2d-129">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="13f2d-130">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="13f2d-131">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="13f2d-132">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="13f2d-133">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="13f2d-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="13f2d-134">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="13f2d-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="13f2d-135">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="13f2d-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="13f2d-136">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="13f2d-137">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="13f2d-137">Storage scenarios</span></span>

<span data-ttu-id="13f2d-138">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="13f2d-138">Common storage options for files include:</span></span>

* <span data-ttu-id="13f2d-139">baza danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-139">Database</span></span>

  * <span data-ttu-id="13f2d-140">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="13f2d-141">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="13f2d-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="13f2d-142">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="13f2d-143">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="13f2d-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="13f2d-144">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-144">For large file uploads:</span></span>
    * <span data-ttu-id="13f2d-145">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="13f2d-146">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="13f2d-147">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="13f2d-148">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="13f2d-149">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="13f2d-150">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="13f2d-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="13f2d-151">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="13f2d-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="13f2d-152">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="13f2d-153">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="13f2d-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="13f2d-154">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-154">File upload scenarios</span></span>

<span data-ttu-id="13f2d-155">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="13f2d-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="13f2d-156">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="13f2d-156">**Buffering**</span></span>

<span data-ttu-id="13f2d-157">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="13f2d-158">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="13f2d-159">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="13f2d-160">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-161">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="13f2d-162">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="13f2d-163">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="13f2d-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="13f2d-164">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="13f2d-164">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="13f2d-165">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="13f2d-165">**Streaming**</span></span>

<span data-ttu-id="13f2d-166">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="13f2d-167">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="13f2d-168">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="13f2d-169">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="13f2d-170">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="13f2d-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="13f2d-171">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="13f2d-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="13f2d-172">Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="13f2d-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="13f2d-173">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="13f2d-174">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="13f2d-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="13f2d-175">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="13f2d-175">There's no validation.</span></span>

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

<span data-ttu-id="13f2d-176">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="13f2d-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="13f2d-177">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="13f2d-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="13f2d-178">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="13f2d-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="13f2d-179">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-179">For example:</span></span>

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

<span data-ttu-id="13f2d-180">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="13f2d-181">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="13f2d-182">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="13f2d-183">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="13f2d-184">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="13f2d-185">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="13f2d-186">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="13f2d-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="13f2d-187">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="13f2d-187">Applications should:</span></span>
>
> * <span data-ttu-id="13f2d-188">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="13f2d-189">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="13f2d-190">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="13f2d-191">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="13f2d-192">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-193">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-194">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-195">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-195">Validation</span></span>](#validation)

<span data-ttu-id="13f2d-196">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="13f2d-197">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="13f2d-198">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="13f2d-199">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="13f2d-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-200">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-200">Binding matches form files by name.</span></span> <span data-ttu-id="13f2d-201">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="13f2d-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="13f2d-202">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="13f2d-203">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-203">The following example:</span></span>

* <span data-ttu-id="13f2d-204">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="13f2d-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="13f2d-205">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="13f2d-206">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="13f2d-207">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="13f2d-208">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="13f2d-209">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="13f2d-210">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="13f2d-211">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="13f2d-212">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="13f2d-213">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="13f2d-214">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="13f2d-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="13f2d-215">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-216">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="13f2d-217">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="13f2d-218">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="13f2d-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="13f2d-219">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="13f2d-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="13f2d-220">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="13f2d-221">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="13f2d-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="13f2d-222">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="13f2d-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="13f2d-223"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="13f2d-224">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="13f2d-225">`FileUpload`Jest używana w Razor formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="13f2d-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="13f2d-226">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="13f2d-227">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="13f2d-228">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="13f2d-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="13f2d-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="13f2d-230">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="13f2d-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-231">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="13f2d-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="13f2d-232">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="13f2d-233">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="13f2d-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="13f2d-234">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-235">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-236">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-237">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="13f2d-238">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="13f2d-238">Upload large files with streaming</span></span>

<span data-ttu-id="13f2d-239">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="13f2d-240">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="13f2d-241">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="13f2d-242">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="13f2d-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="13f2d-243">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="13f2d-244">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="13f2d-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="13f2d-245">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:</span><span class="sxs-lookup"><span data-stu-id="13f2d-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="13f2d-246">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="13f2d-247">W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="13f2d-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="13f2d-248">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="13f2d-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="13f2d-249">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="13f2d-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="13f2d-250">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="13f2d-251">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="13f2d-252">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="13f2d-253">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="13f2d-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="13f2d-254">`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="13f2d-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="13f2d-255">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="13f2d-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="13f2d-256">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="13f2d-257">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-257">Validation</span></span>

<span data-ttu-id="13f2d-258">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="13f2d-259">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="13f2d-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="13f2d-260">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-261">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="13f2d-262">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="13f2d-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="13f2d-263">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="13f2d-264">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="13f2d-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="13f2d-265">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="13f2d-266">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="13f2d-267">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="13f2d-267">Content validation</span></span>

<span data-ttu-id="13f2d-268">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="13f2d-269">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="13f2d-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="13f2d-270">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="13f2d-271">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="13f2d-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="13f2d-272">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="13f2d-273">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="13f2d-274">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="13f2d-275">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-275">File extension validation</span></span>

<span data-ttu-id="13f2d-276">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="13f2d-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="13f2d-277">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="13f2d-278">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-278">File signature validation</span></span>

<span data-ttu-id="13f2d-279">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="13f2d-280">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="13f2d-281">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="13f2d-282">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="13f2d-283">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="13f2d-284">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-284">File name security</span></span>

<span data-ttu-id="13f2d-285">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="13f2d-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="13f2d-286">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="13f2d-287">Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="13f2d-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="13f2d-288">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="13f2d-289">Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="13f2d-290">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="13f2d-291">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="13f2d-292">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="13f2d-292">Size validation</span></span>

<span data-ttu-id="13f2d-293">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="13f2d-294">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="13f2d-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="13f2d-295">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="13f2d-296">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="13f2d-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="13f2d-297">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="13f2d-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="13f2d-298">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="13f2d-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="13f2d-299">W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="13f2d-300">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-300">In the following example:</span></span>

* <span data-ttu-id="13f2d-301">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="13f2d-302">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="13f2d-303">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="13f2d-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="13f2d-304">Dla Razor metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="13f2d-305">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="13f2d-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="13f2d-306">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="13f2d-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="13f2d-307">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="13f2d-307">Multipart body length limit</span></span>

<span data-ttu-id="13f2d-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="13f2d-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="13f2d-309">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="13f2d-310">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="13f2d-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="13f2d-311">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="13f2d-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="13f2d-313">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="13f2d-314">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="13f2d-315">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="13f2d-316">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-317">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="13f2d-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="13f2d-319">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="13f2d-320">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="13f2d-321">`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) Razor dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="13f2d-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="13f2d-322">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-322">Other Kestrel limits</span></span>

<span data-ttu-id="13f2d-323">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="13f2d-324">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="13f2d-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="13f2d-325">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="13f2d-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="13f2d-326">IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-326">IIS</span></span>

<span data-ttu-id="13f2d-327">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-328">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="13f2d-329">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="13f2d-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="13f2d-330">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="13f2d-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="13f2d-331">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="13f2d-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="13f2d-332">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="13f2d-332">Troubleshoot</span></span>

<span data-ttu-id="13f2d-333">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="13f2d-334">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="13f2d-335">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="13f2d-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="13f2d-336">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="13f2d-337">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="13f2d-337">Connection failure</span></span>

<span data-ttu-id="13f2d-338">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="13f2d-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="13f2d-339">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="13f2d-340">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="13f2d-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="13f2d-341">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="13f2d-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="13f2d-342">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="13f2d-343">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="13f2d-344">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="13f2d-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="13f2d-345">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="13f2d-345">Stream was too long</span></span>

<span data-ttu-id="13f2d-346">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="13f2d-347">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="13f2d-348">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="13f2d-349">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="13f2d-350">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13f2d-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="13f2d-351">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-351">Security considerations</span></span>

<span data-ttu-id="13f2d-352">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="13f2d-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="13f2d-353">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="13f2d-354">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="13f2d-355">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="13f2d-356">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="13f2d-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="13f2d-357">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="13f2d-358">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="13f2d-359">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="13f2d-360">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="13f2d-361">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="13f2d-362">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="13f2d-363">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="13f2d-364">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="13f2d-365">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="13f2d-366">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="13f2d-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="13f2d-367">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="13f2d-368">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="13f2d-369">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="13f2d-370">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="13f2d-371">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="13f2d-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-372">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="13f2d-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="13f2d-373">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="13f2d-374">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="13f2d-375">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="13f2d-376">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="13f2d-377">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="13f2d-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="13f2d-378">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="13f2d-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="13f2d-379">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="13f2d-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="13f2d-380">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="13f2d-381">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="13f2d-381">Storage scenarios</span></span>

<span data-ttu-id="13f2d-382">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="13f2d-382">Common storage options for files include:</span></span>

* <span data-ttu-id="13f2d-383">baza danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-383">Database</span></span>

  * <span data-ttu-id="13f2d-384">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="13f2d-385">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="13f2d-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="13f2d-386">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="13f2d-387">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="13f2d-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="13f2d-388">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-388">For large file uploads:</span></span>
    * <span data-ttu-id="13f2d-389">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="13f2d-390">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="13f2d-391">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="13f2d-392">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="13f2d-393">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="13f2d-394">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="13f2d-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="13f2d-395">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="13f2d-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="13f2d-396">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="13f2d-397">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="13f2d-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="13f2d-398">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-398">File upload scenarios</span></span>

<span data-ttu-id="13f2d-399">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="13f2d-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="13f2d-400">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="13f2d-400">**Buffering**</span></span>

<span data-ttu-id="13f2d-401">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="13f2d-402">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="13f2d-403">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="13f2d-404">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-405">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="13f2d-406">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="13f2d-407">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="13f2d-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="13f2d-408">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="13f2d-408">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="13f2d-409">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="13f2d-409">**Streaming**</span></span>

<span data-ttu-id="13f2d-410">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="13f2d-411">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="13f2d-412">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="13f2d-413">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="13f2d-414">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="13f2d-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="13f2d-415">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="13f2d-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="13f2d-416">Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="13f2d-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="13f2d-417">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="13f2d-418">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="13f2d-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="13f2d-419">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="13f2d-419">There's no validation.</span></span>

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

<span data-ttu-id="13f2d-420">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="13f2d-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="13f2d-421">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="13f2d-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="13f2d-422">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="13f2d-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="13f2d-423">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-423">For example:</span></span>

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

<span data-ttu-id="13f2d-424">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="13f2d-425">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="13f2d-426">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="13f2d-427">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="13f2d-428">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="13f2d-429">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="13f2d-430">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="13f2d-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="13f2d-431">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="13f2d-431">Applications should:</span></span>
>
> * <span data-ttu-id="13f2d-432">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="13f2d-433">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="13f2d-434">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="13f2d-435">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="13f2d-436">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-437">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-438">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-439">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-439">Validation</span></span>](#validation)

<span data-ttu-id="13f2d-440">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="13f2d-441">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="13f2d-442">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="13f2d-443">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="13f2d-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-444">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-444">Binding matches form files by name.</span></span> <span data-ttu-id="13f2d-445">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="13f2d-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="13f2d-446">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="13f2d-447">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-447">The following example:</span></span>

* <span data-ttu-id="13f2d-448">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="13f2d-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="13f2d-449">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="13f2d-450">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="13f2d-451">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="13f2d-452">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="13f2d-453">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="13f2d-454">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="13f2d-455">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="13f2d-456">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="13f2d-457">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="13f2d-458">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="13f2d-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="13f2d-459">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-460">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="13f2d-461">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="13f2d-462">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="13f2d-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="13f2d-463">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="13f2d-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="13f2d-464">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="13f2d-465">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="13f2d-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="13f2d-466">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="13f2d-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="13f2d-467"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="13f2d-468">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="13f2d-469">`FileUpload`Jest używana w Razor formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="13f2d-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="13f2d-470">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="13f2d-471">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="13f2d-472">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="13f2d-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="13f2d-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="13f2d-474">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="13f2d-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-475">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="13f2d-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="13f2d-476">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="13f2d-477">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="13f2d-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="13f2d-478">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-479">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-480">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-481">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="13f2d-482">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="13f2d-482">Upload large files with streaming</span></span>

<span data-ttu-id="13f2d-483">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="13f2d-484">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="13f2d-485">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="13f2d-486">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="13f2d-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="13f2d-487">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="13f2d-488">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="13f2d-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="13f2d-489">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:</span><span class="sxs-lookup"><span data-stu-id="13f2d-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="13f2d-490">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="13f2d-491">W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="13f2d-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="13f2d-492">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="13f2d-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="13f2d-493">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="13f2d-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="13f2d-494">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="13f2d-495">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="13f2d-496">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="13f2d-497">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="13f2d-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="13f2d-498">`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="13f2d-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="13f2d-499">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="13f2d-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="13f2d-500">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="13f2d-501">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-501">Validation</span></span>

<span data-ttu-id="13f2d-502">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="13f2d-503">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="13f2d-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="13f2d-504">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-505">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="13f2d-506">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="13f2d-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="13f2d-507">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="13f2d-508">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="13f2d-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="13f2d-509">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="13f2d-510">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="13f2d-511">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="13f2d-511">Content validation</span></span>

<span data-ttu-id="13f2d-512">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="13f2d-513">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="13f2d-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="13f2d-514">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="13f2d-515">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="13f2d-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="13f2d-516">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="13f2d-517">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="13f2d-518">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="13f2d-519">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-519">File extension validation</span></span>

<span data-ttu-id="13f2d-520">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="13f2d-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="13f2d-521">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="13f2d-522">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-522">File signature validation</span></span>

<span data-ttu-id="13f2d-523">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="13f2d-524">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="13f2d-525">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="13f2d-526">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="13f2d-527">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="13f2d-528">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-528">File name security</span></span>

<span data-ttu-id="13f2d-529">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="13f2d-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="13f2d-530">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="13f2d-531">Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="13f2d-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="13f2d-532">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="13f2d-533">Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="13f2d-534">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="13f2d-535">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="13f2d-536">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="13f2d-536">Size validation</span></span>

<span data-ttu-id="13f2d-537">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="13f2d-538">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="13f2d-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="13f2d-539">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="13f2d-540">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="13f2d-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="13f2d-541">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="13f2d-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="13f2d-542">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="13f2d-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="13f2d-543">W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="13f2d-544">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-544">In the following example:</span></span>

* <span data-ttu-id="13f2d-545">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="13f2d-546">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="13f2d-547">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="13f2d-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="13f2d-548">Dla Razor metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="13f2d-549">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="13f2d-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="13f2d-550">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="13f2d-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="13f2d-551">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="13f2d-551">Multipart body length limit</span></span>

<span data-ttu-id="13f2d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="13f2d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="13f2d-553">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="13f2d-554">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="13f2d-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="13f2d-555">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="13f2d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="13f2d-557">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="13f2d-558">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="13f2d-559">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="13f2d-560">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-561">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="13f2d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="13f2d-563">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="13f2d-564">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="13f2d-565">`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) Razor dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="13f2d-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="13f2d-566">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-566">Other Kestrel limits</span></span>

<span data-ttu-id="13f2d-567">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="13f2d-568">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="13f2d-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="13f2d-569">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="13f2d-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="13f2d-570">IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-570">IIS</span></span>

<span data-ttu-id="13f2d-571">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-572">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="13f2d-573">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="13f2d-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="13f2d-574">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="13f2d-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="13f2d-575">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="13f2d-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="13f2d-576">Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13f2d-577">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="13f2d-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="13f2d-578">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="13f2d-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="13f2d-579">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="13f2d-579">Troubleshoot</span></span>

<span data-ttu-id="13f2d-580">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="13f2d-581">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="13f2d-582">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="13f2d-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="13f2d-583">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="13f2d-584">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="13f2d-584">Connection failure</span></span>

<span data-ttu-id="13f2d-585">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="13f2d-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="13f2d-586">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="13f2d-587">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="13f2d-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="13f2d-588">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="13f2d-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="13f2d-589">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="13f2d-590">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="13f2d-591">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="13f2d-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="13f2d-592">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="13f2d-592">Stream was too long</span></span>

<span data-ttu-id="13f2d-593">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="13f2d-594">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="13f2d-595">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="13f2d-596">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="13f2d-597">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13f2d-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="13f2d-598">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-598">Security considerations</span></span>

<span data-ttu-id="13f2d-599">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="13f2d-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="13f2d-600">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="13f2d-601">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="13f2d-602">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="13f2d-603">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="13f2d-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="13f2d-604">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="13f2d-605">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="13f2d-606">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="13f2d-607">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="13f2d-608">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="13f2d-609">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="13f2d-610">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="13f2d-611">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="13f2d-612">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="13f2d-613">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="13f2d-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="13f2d-614">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="13f2d-615">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="13f2d-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="13f2d-616">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="13f2d-617">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="13f2d-618">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="13f2d-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-619">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="13f2d-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="13f2d-620">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="13f2d-621">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="13f2d-622">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="13f2d-623">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="13f2d-624">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="13f2d-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="13f2d-625">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="13f2d-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="13f2d-626">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="13f2d-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="13f2d-627">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="13f2d-628">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="13f2d-628">Storage scenarios</span></span>

<span data-ttu-id="13f2d-629">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="13f2d-629">Common storage options for files include:</span></span>

* <span data-ttu-id="13f2d-630">baza danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-630">Database</span></span>

  * <span data-ttu-id="13f2d-631">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="13f2d-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="13f2d-632">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="13f2d-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="13f2d-633">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="13f2d-634">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="13f2d-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="13f2d-635">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-635">For large file uploads:</span></span>
    * <span data-ttu-id="13f2d-636">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="13f2d-637">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="13f2d-638">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="13f2d-639">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="13f2d-640">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="13f2d-641">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="13f2d-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="13f2d-642">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="13f2d-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="13f2d-643">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="13f2d-644">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="13f2d-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="13f2d-645">Temat ilustruje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> może służyć do zapisywania <xref:System.IO.FileStream> do magazynu obiektów BLOB podczas pracy z <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="13f2d-646">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-646">File upload scenarios</span></span>

<span data-ttu-id="13f2d-647">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="13f2d-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="13f2d-648">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="13f2d-648">**Buffering**</span></span>

<span data-ttu-id="13f2d-649">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="13f2d-650">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="13f2d-651">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="13f2d-652">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-653">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="13f2d-654">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="13f2d-655">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="13f2d-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="13f2d-656">[Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)</span><span class="sxs-lookup"><span data-stu-id="13f2d-656">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="13f2d-657">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="13f2d-657">**Streaming**</span></span>

<span data-ttu-id="13f2d-658">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="13f2d-659">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="13f2d-660">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="13f2d-661">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="13f2d-662">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="13f2d-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="13f2d-663">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="13f2d-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="13f2d-664">Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="13f2d-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="13f2d-665">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="13f2d-666">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="13f2d-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="13f2d-667">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="13f2d-667">There's no validation.</span></span>

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

<span data-ttu-id="13f2d-668">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="13f2d-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="13f2d-669">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="13f2d-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="13f2d-670">Użyj polecenia `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="13f2d-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="13f2d-671">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-671">For example:</span></span>

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

<span data-ttu-id="13f2d-672">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="13f2d-673">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="13f2d-674">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="13f2d-675">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="13f2d-676">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="13f2d-677">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="13f2d-678">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="13f2d-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="13f2d-679">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="13f2d-679">Applications should:</span></span>
>
> * <span data-ttu-id="13f2d-680">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="13f2d-681">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="13f2d-682">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="13f2d-683">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="13f2d-684">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-685">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-686">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-687">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-687">Validation</span></span>](#validation)

<span data-ttu-id="13f2d-688">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="13f2d-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="13f2d-689">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="13f2d-690">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="13f2d-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="13f2d-691">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="13f2d-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="13f2d-692">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-692">Binding matches form files by name.</span></span> <span data-ttu-id="13f2d-693">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="13f2d-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="13f2d-694">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="13f2d-695">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-695">The following example:</span></span>

* <span data-ttu-id="13f2d-696">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="13f2d-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="13f2d-697">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="13f2d-698">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="13f2d-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="13f2d-699">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="13f2d-700">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="13f2d-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="13f2d-701">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="13f2d-702">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="13f2d-703">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="13f2d-704">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="13f2d-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="13f2d-705">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="13f2d-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="13f2d-706">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="13f2d-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="13f2d-707">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-708">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="13f2d-709">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="13f2d-710">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="13f2d-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="13f2d-711">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="13f2d-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="13f2d-712">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="13f2d-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="13f2d-713">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="13f2d-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="13f2d-714">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="13f2d-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="13f2d-715"><xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="13f2d-716">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="13f2d-717">`FileUpload`Jest używana w Razor formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="13f2d-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="13f2d-718">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="13f2d-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="13f2d-719">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="13f2d-720">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="13f2d-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="13f2d-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="13f2d-722">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="13f2d-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-723">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="13f2d-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="13f2d-724">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="13f2d-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="13f2d-725">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="13f2d-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="13f2d-726">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="13f2d-727">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="13f2d-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="13f2d-728">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="13f2d-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="13f2d-729">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="13f2d-730">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="13f2d-730">Upload large files with streaming</span></span>

<span data-ttu-id="13f2d-731">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="13f2d-732">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="13f2d-733">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="13f2d-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="13f2d-734">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="13f2d-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="13f2d-735">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="13f2d-736">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="13f2d-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="13f2d-737">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:</span><span class="sxs-lookup"><span data-stu-id="13f2d-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="13f2d-738">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="13f2d-739">W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="13f2d-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="13f2d-740">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="13f2d-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="13f2d-741">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="13f2d-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="13f2d-742">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="13f2d-743">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="13f2d-744">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="13f2d-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="13f2d-745">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="13f2d-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="13f2d-746">`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="13f2d-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="13f2d-747">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="13f2d-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="13f2d-748">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="13f2d-749">Walidacja</span><span class="sxs-lookup"><span data-stu-id="13f2d-749">Validation</span></span>

<span data-ttu-id="13f2d-750">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="13f2d-751">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="13f2d-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="13f2d-752">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="13f2d-753">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="13f2d-754">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="13f2d-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="13f2d-755">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="13f2d-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="13f2d-756">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="13f2d-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="13f2d-757">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="13f2d-758">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="13f2d-759">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="13f2d-759">Content validation</span></span>

<span data-ttu-id="13f2d-760">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="13f2d-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="13f2d-761">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="13f2d-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="13f2d-762">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="13f2d-763">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="13f2d-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="13f2d-764">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="13f2d-765">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="13f2d-766">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="13f2d-767">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-767">File extension validation</span></span>

<span data-ttu-id="13f2d-768">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="13f2d-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="13f2d-769">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="13f2d-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="13f2d-770">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="13f2d-770">File signature validation</span></span>

<span data-ttu-id="13f2d-771">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="13f2d-772">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="13f2d-773">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="13f2d-774">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="13f2d-775">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="13f2d-776">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="13f2d-776">File name security</span></span>

<span data-ttu-id="13f2d-777">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="13f2d-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="13f2d-778">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="13f2d-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="13f2d-779">Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="13f2d-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="13f2d-780">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="13f2d-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="13f2d-781">Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="13f2d-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="13f2d-782">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="13f2d-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="13f2d-783">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="13f2d-784">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="13f2d-784">Size validation</span></span>

<span data-ttu-id="13f2d-785">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="13f2d-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="13f2d-786">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="13f2d-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="13f2d-787">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="13f2d-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="13f2d-788">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="13f2d-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="13f2d-789">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="13f2d-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="13f2d-790">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="13f2d-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="13f2d-791">W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="13f2d-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="13f2d-792">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="13f2d-792">In the following example:</span></span>

* <span data-ttu-id="13f2d-793">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="13f2d-794">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="13f2d-795">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="13f2d-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="13f2d-796">Dla Razor metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="13f2d-797">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="13f2d-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="13f2d-798">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="13f2d-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="13f2d-799">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="13f2d-799">Multipart body length limit</span></span>

<span data-ttu-id="13f2d-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="13f2d-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="13f2d-801">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="13f2d-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="13f2d-802">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="13f2d-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="13f2d-803">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="13f2d-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="13f2d-805">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
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

<span data-ttu-id="13f2d-806">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="13f2d-807">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="13f2d-808">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-809">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="13f2d-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="13f2d-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="13f2d-811">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13f2d-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
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

<span data-ttu-id="13f2d-812">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="13f2d-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="13f2d-813">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="13f2d-813">Other Kestrel limits</span></span>

<span data-ttu-id="13f2d-814">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="13f2d-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="13f2d-815">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="13f2d-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="13f2d-816">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="13f2d-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="13f2d-817">IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-817">IIS</span></span>

<span data-ttu-id="13f2d-818">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="13f2d-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="13f2d-819">Dostosuj limit w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="13f2d-820">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="13f2d-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="13f2d-821">To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="13f2d-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="13f2d-822">Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="13f2d-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="13f2d-823">Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13f2d-824">W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):</span><span class="sxs-lookup"><span data-stu-id="13f2d-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="13f2d-825">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="13f2d-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="13f2d-826">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="13f2d-826">Troubleshoot</span></span>

<span data-ttu-id="13f2d-827">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="13f2d-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="13f2d-828">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="13f2d-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="13f2d-829">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="13f2d-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="13f2d-830">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="13f2d-831">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="13f2d-831">Connection failure</span></span>

<span data-ttu-id="13f2d-832">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="13f2d-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="13f2d-833">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="13f2d-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="13f2d-834">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="13f2d-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="13f2d-835">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="13f2d-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="13f2d-836">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="13f2d-837">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="13f2d-838">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="13f2d-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="13f2d-839">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="13f2d-839">Stream was too long</span></span>

<span data-ttu-id="13f2d-840">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="13f2d-841">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="13f2d-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="13f2d-842">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="13f2d-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="13f2d-843">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="13f2d-843">Additional resources</span></span>

* [<span data-ttu-id="13f2d-844">Opróżnianie żądań połączenia HTTP</span><span class="sxs-lookup"><span data-stu-id="13f2d-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="13f2d-845">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="13f2d-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="13f2d-846">Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Środki zaradcze</span><span class="sxs-lookup"><span data-stu-id="13f2d-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="13f2d-847">Wzorce projektowe chmury platformy Azure: wzorzec klucza portiera</span><span class="sxs-lookup"><span data-stu-id="13f2d-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
