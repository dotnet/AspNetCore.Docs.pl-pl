---
<span data-ttu-id="ab6d3-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab6d3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab6d3-102">'Blazor'</span></span>
- <span data-ttu-id="ab6d3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab6d3-103">'Identity'</span></span>
- <span data-ttu-id="ab6d3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab6d3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab6d3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab6d3-105">'Razor'</span></span>
- <span data-ttu-id="ab6d3-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="ab6d3-107">Przekaż pliki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab6d3-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="ab6d3-108">Przez [Steve Kowalski](https://ardalis.com/) i [Rutger burzy](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="ab6d3-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab6d3-109">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ab6d3-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab6d3-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ab6d3-111">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-111">Security considerations</span></span>

<span data-ttu-id="ab6d3-112">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ab6d3-113">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="ab6d3-114">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ab6d3-115">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="ab6d3-116">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ab6d3-117">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ab6d3-118">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ab6d3-119">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ab6d3-120">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ab6d3-121">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ab6d3-122">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ab6d3-123">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ab6d3-124">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ab6d3-125">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ab6d3-126">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ab6d3-127">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="ab6d3-128">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ab6d3-129">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ab6d3-130">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ab6d3-131">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-132">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ab6d3-133">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ab6d3-134">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ab6d3-135">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="ab6d3-136">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ab6d3-137">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ab6d3-138">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="ab6d3-138">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ab6d3-139">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="ab6d3-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ab6d3-140">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ab6d3-141">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="ab6d3-141">Storage scenarios</span></span>

<span data-ttu-id="ab6d3-142">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-142">Common storage options for files include:</span></span>

* <span data-ttu-id="ab6d3-143">baza danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-143">Database</span></span>

  * <span data-ttu-id="ab6d3-144">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ab6d3-145">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ab6d3-146">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ab6d3-147">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="ab6d3-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ab6d3-148">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-148">For large file uploads:</span></span>
    * <span data-ttu-id="ab6d3-149">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ab6d3-150">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ab6d3-151">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ab6d3-152">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ab6d3-153">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ab6d3-154">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="ab6d3-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ab6d3-155">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ab6d3-156">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ab6d3-157">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ab6d3-158">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="ab6d3-158">File upload scenarios</span></span>

<span data-ttu-id="ab6d3-159">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ab6d3-160">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-160">**Buffering**</span></span>

<span data-ttu-id="ab6d3-161">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ab6d3-162">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ab6d3-163">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ab6d3-164">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ab6d3-165">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ab6d3-166">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ab6d3-167">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="ab6d3-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ab6d3-168">baza danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ab6d3-169">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-169">**Streaming**</span></span>

<span data-ttu-id="ab6d3-170">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ab6d3-171">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ab6d3-172">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ab6d3-173">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ab6d3-174">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="ab6d3-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ab6d3-175">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ab6d3-176">Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ab6d3-177">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ab6d3-178">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ab6d3-179">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-179">There's no validation.</span></span>

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

<span data-ttu-id="ab6d3-180">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ab6d3-181">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ab6d3-182">Użyj witryny `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ab6d3-183">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-183">For example:</span></span>

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

<span data-ttu-id="ab6d3-184">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ab6d3-185">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ab6d3-186">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ab6d3-187">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="ab6d3-188">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ab6d3-189">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ab6d3-190">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ab6d3-191">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-191">Applications should:</span></span>
>
> * <span data-ttu-id="ab6d3-192">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ab6d3-193">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ab6d3-194">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ab6d3-195">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ab6d3-196">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ab6d3-197">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ab6d3-198">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ab6d3-199">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-199">Validation</span></span>](#validation)

<span data-ttu-id="ab6d3-200">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ab6d3-201">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ab6d3-202">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ab6d3-203">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ab6d3-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ab6d3-204">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-204">Binding matches form files by name.</span></span> <span data-ttu-id="ab6d3-205">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ab6d3-206">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ab6d3-207">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-207">The following example:</span></span>

* <span data-ttu-id="ab6d3-208">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ab6d3-209">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ab6d3-210">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ab6d3-211">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-211">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ab6d3-212">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ab6d3-213">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ab6d3-214">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ab6d3-215">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ab6d3-216">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ab6d3-217">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ab6d3-218">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ab6d3-219">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-220">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ab6d3-221">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ab6d3-222">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="ab6d3-223">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ab6d3-224">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ab6d3-225">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ab6d3-226">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ab6d3-227"><xref:Microsoft.AspNetCore.Http.IFormFile>można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ab6d3-228">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ab6d3-229">`FileUpload`Jest używana w Razor formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ab6d3-230">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ab6d3-231">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ab6d3-232">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ab6d3-233">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab6d3-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ab6d3-234">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="ab6d3-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-235">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ab6d3-236">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ab6d3-237">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ab6d3-238">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ab6d3-239">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ab6d3-240">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ab6d3-241">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ab6d3-242">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="ab6d3-242">Upload large files with streaming</span></span>

<span data-ttu-id="ab6d3-243">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ab6d3-244">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ab6d3-245">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ab6d3-246">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ab6d3-247">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ab6d3-248">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w pliku cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ab6d3-249">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić plik cookie z tokenem żądania:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ab6d3-250">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ab6d3-251">W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="ab6d3-252">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ab6d3-253">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ab6d3-254">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ab6d3-255">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ab6d3-256">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ab6d3-257">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ab6d3-258">`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ab6d3-259">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ab6d3-260">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ab6d3-261">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-261">Validation</span></span>

<span data-ttu-id="ab6d3-262">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ab6d3-263">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ab6d3-264">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-265">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ab6d3-266">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ab6d3-267">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ab6d3-268">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ab6d3-269">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ab6d3-270">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ab6d3-271">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="ab6d3-271">Content validation</span></span>

<span data-ttu-id="ab6d3-272">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ab6d3-273">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ab6d3-274">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ab6d3-275">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ab6d3-276">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ab6d3-277">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ab6d3-278">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ab6d3-279">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="ab6d3-279">File extension validation</span></span>

<span data-ttu-id="ab6d3-280">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ab6d3-281">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ab6d3-282">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="ab6d3-282">File signature validation</span></span>

<span data-ttu-id="ab6d3-283">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ab6d3-284">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ab6d3-285">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ab6d3-286">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ab6d3-287">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ab6d3-288">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="ab6d3-288">File name security</span></span>

<span data-ttu-id="ab6d3-289">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ab6d3-290">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="ab6d3-291">automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ab6d3-292">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ab6d3-293">Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ab6d3-294">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ab6d3-295">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ab6d3-296">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="ab6d3-296">Size validation</span></span>

<span data-ttu-id="ab6d3-297">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ab6d3-298">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ab6d3-299">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ab6d3-300">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ab6d3-301">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ab6d3-302">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="ab6d3-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ab6d3-303">W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ab6d3-304">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-304">In the following example:</span></span>

* <span data-ttu-id="ab6d3-305">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ab6d3-306">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ab6d3-307">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ab6d3-308">Dla Razor metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ab6d3-309">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ab6d3-310">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="ab6d3-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ab6d3-311">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="ab6d3-311">Multipart body length limit</span></span>

<span data-ttu-id="ab6d3-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ab6d3-313">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ab6d3-314">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ab6d3-315">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ab6d3-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ab6d3-317">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="ab6d3-318">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ab6d3-319">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="ab6d3-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="ab6d3-320">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ab6d3-321">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ab6d3-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ab6d3-323">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="ab6d3-324">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="ab6d3-325">`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) Razor dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ab6d3-326">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="ab6d3-326">Other Kestrel limits</span></span>

<span data-ttu-id="ab6d3-327">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ab6d3-328">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="ab6d3-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ab6d3-329">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ab6d3-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ab6d3-330">Limit długości zawartości usług IIS</span><span class="sxs-lookup"><span data-stu-id="ab6d3-330">IIS content length limit</span></span>

<span data-ttu-id="ab6d3-331">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ab6d3-332">Dostosuj limit w pliku *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-332">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ab6d3-333">To ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-333">This setting only applies to IIS.</span></span> <span data-ttu-id="ab6d3-334">Zachowanie nie występuje domyślnie podczas hostowania w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ab6d3-335">Aby uzyskać więcej informacji, zobacz [limity żądań \< requestlimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ab6d3-336">Ograniczenia w module ASP.NET Core lub obecność modułu filtrowania żądań usług IIS mogą ograniczyć przekazywanie do 2 lub 4 GB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ab6d3-337">Aby uzyskać więcej informacji, zobacz [nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ab6d3-338">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="ab6d3-338">Troubleshoot</span></span>

<span data-ttu-id="ab6d3-339">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ab6d3-340">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="ab6d3-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ab6d3-341">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ab6d3-342">Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usług IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ab6d3-343">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="ab6d3-343">Connection failure</span></span>

<span data-ttu-id="ab6d3-344">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ab6d3-345">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ab6d3-346">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ab6d3-347">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="ab6d3-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ab6d3-348">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ab6d3-349">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ab6d3-350">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ab6d3-351">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="ab6d3-351">Stream was too long</span></span>

<span data-ttu-id="ab6d3-352">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ab6d3-353">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ab6d3-354">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab6d3-355">ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ab6d3-356">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab6d3-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ab6d3-357">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-357">Security considerations</span></span>

<span data-ttu-id="ab6d3-358">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ab6d3-359">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="ab6d3-360">Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ab6d3-361">Przekazuj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="ab6d3-362">Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ab6d3-363">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ab6d3-364">Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ab6d3-365">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ab6d3-366">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ab6d3-367">**Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ab6d3-368">Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ab6d3-369">Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ab6d3-370">Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ab6d3-371">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ab6d3-372">Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ab6d3-373">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="ab6d3-374">Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ab6d3-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ab6d3-375">Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ab6d3-376">**Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ab6d3-377">&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-378">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ab6d3-379">Całkowicie przejąć kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ab6d3-380">Przeciąż system z wynikiem awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ab6d3-381">Naruszanie danych użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="ab6d3-382">Zastosuj graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ab6d3-383">Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ab6d3-384">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="ab6d3-384">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ab6d3-385">Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki</span><span class="sxs-lookup"><span data-stu-id="ab6d3-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ab6d3-386">Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ab6d3-387">Scenariusze magazynu</span><span class="sxs-lookup"><span data-stu-id="ab6d3-387">Storage scenarios</span></span>

<span data-ttu-id="ab6d3-388">Typowe opcje magazynu dla plików to:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-388">Common storage options for files include:</span></span>

* <span data-ttu-id="ab6d3-389">baza danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-389">Database</span></span>

  * <span data-ttu-id="ab6d3-390">W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ab6d3-391">Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ab6d3-392">Baza danych może być tańsza niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ab6d3-393">Magazyn fizyczny (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="ab6d3-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ab6d3-394">W przypadku dużych operacji przekazywania plików:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-394">For large file uploads:</span></span>
    * <span data-ttu-id="ab6d3-395">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ab6d3-396">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ab6d3-397">Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ab6d3-398">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ab6d3-399">**Nigdy nie udzielaj uprawnienia EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ab6d3-400">Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="ab6d3-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ab6d3-401">Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ab6d3-402">Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ab6d3-403">Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="ab6d3-404">Temat ilustruje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> może służyć do zapisywania <xref:System.IO.FileStream> do magazynu obiektów BLOB podczas pracy z <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ab6d3-405">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="ab6d3-405">File upload scenarios</span></span>

<span data-ttu-id="ab6d3-406">Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ab6d3-407">**Buforowania**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-407">**Buffering**</span></span>

<span data-ttu-id="ab6d3-408">Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ab6d3-409">Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ab6d3-410">Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ab6d3-411">Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ab6d3-412">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ab6d3-413">Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ab6d3-414">Magazyn fizyczny</span><span class="sxs-lookup"><span data-stu-id="ab6d3-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ab6d3-415">baza danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ab6d3-416">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-416">**Streaming**</span></span>

<span data-ttu-id="ab6d3-417">Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ab6d3-418">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ab6d3-419">Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ab6d3-420">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ab6d3-421">Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym</span><span class="sxs-lookup"><span data-stu-id="ab6d3-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ab6d3-422">Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ab6d3-423">Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ab6d3-424">Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ab6d3-425">Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ab6d3-426">Nie istnieje weryfikacja.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-426">There's no validation.</span></span>

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

<span data-ttu-id="ab6d3-427">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ab6d3-428">Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ab6d3-429">Użyj witryny `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ab6d3-430">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-430">For example:</span></span>

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

<span data-ttu-id="ab6d3-431">W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ab6d3-432">Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ab6d3-433">Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ab6d3-434">Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="ab6d3-435">**Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ab6d3-436">Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ab6d3-437">Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ab6d3-438">Aplikacje powinny:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-438">Applications should:</span></span>
>
> * <span data-ttu-id="ab6d3-439">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ab6d3-440">Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ab6d3-441">Wygeneruj nową losową nazwę pliku dla magazynu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ab6d3-442">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ab6d3-443">Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ab6d3-444">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ab6d3-445">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ab6d3-446">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-446">Validation</span></span>](#validation)

<span data-ttu-id="ab6d3-447">Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ab6d3-448">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ab6d3-449">Dowolne z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ab6d3-450">[Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ab6d3-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ab6d3-451">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-451">Binding matches form files by name.</span></span> <span data-ttu-id="ab6d3-452">Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ab6d3-453">Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ab6d3-454">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-454">The following example:</span></span>

* <span data-ttu-id="ab6d3-455">Pętle przez co najmniej jeden przekazany plik.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ab6d3-456">Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ab6d3-457">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ab6d3-458">Zwraca łączną liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-458">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ab6d3-459">Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ab6d3-460">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ab6d3-461">Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ab6d3-462">Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ab6d3-463">Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ab6d3-464">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ab6d3-465">Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ab6d3-466">Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-467">[Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ab6d3-468">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ab6d3-469">Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="ab6d3-470">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ab6d3-471">Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych</span><span class="sxs-lookup"><span data-stu-id="ab6d3-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ab6d3-472">Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ab6d3-473">Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ab6d3-474"><xref:Microsoft.AspNetCore.Http.IFormFile>można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ab6d3-475">W poprzednim przykładzie użyto powiązanej właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ab6d3-476">`FileUpload`Jest używana w Razor formularzu stron:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ab6d3-477">Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ab6d3-478">W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ab6d3-479">Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ab6d3-480">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab6d3-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ab6d3-481">*Strony/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="ab6d3-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-482">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ab6d3-483">Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ab6d3-484">`FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ab6d3-485">Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ab6d3-486">Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ab6d3-487">Zagadnienia związane z zabezpieczeniami</span><span class="sxs-lookup"><span data-stu-id="ab6d3-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ab6d3-488">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ab6d3-489">Przekazywanie dużych plików strumieniowo</span><span class="sxs-lookup"><span data-stu-id="ab6d3-489">Upload large files with streaming</span></span>

<span data-ttu-id="ab6d3-490">Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ab6d3-491">Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ab6d3-492">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ab6d3-493">W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ab6d3-494">Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ab6d3-495">Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w pliku cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ab6d3-496">Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić plik cookie z tokenem żądania:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ab6d3-497">Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ab6d3-498">W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="ab6d3-499">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ab6d3-500">Metoda akcji działa bezpośrednio z `Request` właściwością.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ab6d3-501">`MultipartReader`Jest używany do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ab6d3-502">Dane klucza/wartości są przechowywane w `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ab6d3-503">Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ab6d3-504">Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ab6d3-505">`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ab6d3-506">Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ab6d3-507">W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ab6d3-508">Walidacja</span><span class="sxs-lookup"><span data-stu-id="ab6d3-508">Validation</span></span>

<span data-ttu-id="ab6d3-509">Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ab6d3-510">Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ab6d3-511">Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ab6d3-512">Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ab6d3-513">W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ab6d3-514">Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ab6d3-515">W pełni zapoznaj się z implementacją.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ab6d3-516">Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ab6d3-517">**Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ab6d3-518">Weryfikacja zawartości</span><span class="sxs-lookup"><span data-stu-id="ab6d3-518">Content validation</span></span>

<span data-ttu-id="ab6d3-519">**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**</span><span class="sxs-lookup"><span data-stu-id="ab6d3-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ab6d3-520">Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ab6d3-521">Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ab6d3-522">Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ab6d3-523">Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ab6d3-524">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ab6d3-525">Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ab6d3-526">Weryfikacja rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="ab6d3-526">File extension validation</span></span>

<span data-ttu-id="ab6d3-527">Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ab6d3-528">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ab6d3-529">Walidacja podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="ab6d3-529">File signature validation</span></span>

<span data-ttu-id="ab6d3-530">Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ab6d3-531">Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ab6d3-532">Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ab6d3-533">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ab6d3-534">Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ab6d3-535">Zabezpieczenia nazw plików</span><span class="sxs-lookup"><span data-stu-id="ab6d3-535">File name security</span></span>

<span data-ttu-id="ab6d3-536">Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ab6d3-537">Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="ab6d3-538">automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ab6d3-539">Następujący kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ab6d3-540">Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ab6d3-541">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ab6d3-542">Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ab6d3-543">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="ab6d3-543">Size validation</span></span>

<span data-ttu-id="ab6d3-544">Ogranicz rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ab6d3-545">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ab6d3-546">Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ab6d3-547">`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ab6d3-548">Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ab6d3-549">Dopasuj wartość atrybutu Name do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="ab6d3-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ab6d3-550">W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ab6d3-551">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-551">In the following example:</span></span>

* <span data-ttu-id="ab6d3-552">W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ab6d3-553">W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ab6d3-554">Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="ab6d3-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ab6d3-555">Dla Razor metody obsługi strony strony o nazwie `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ab6d3-556">Dla metody akcji składnika MVC Controller:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ab6d3-557">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="ab6d3-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ab6d3-558">Limit długości treści wieloczęściowej</span><span class="sxs-lookup"><span data-stu-id="ab6d3-558">Multipart body length limit</span></span>

<span data-ttu-id="ab6d3-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdej wieloczęściowej treści.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ab6d3-560">Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ab6d3-561">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ab6d3-562">Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ab6d3-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ab6d3-564">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ab6d3-565">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ab6d3-566">Maksymalny rozmiar treści żądania Kestrel</span><span class="sxs-lookup"><span data-stu-id="ab6d3-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="ab6d3-567">W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ab6d3-568">Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ab6d3-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ab6d3-570">W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ab6d3-571">W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ab6d3-572">Inne limity Kestrel</span><span class="sxs-lookup"><span data-stu-id="ab6d3-572">Other Kestrel limits</span></span>

<span data-ttu-id="ab6d3-573">Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ab6d3-574">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="ab6d3-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ab6d3-575">Stawki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ab6d3-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ab6d3-576">Limit długości zawartości usług IIS</span><span class="sxs-lookup"><span data-stu-id="ab6d3-576">IIS content length limit</span></span>

<span data-ttu-id="ab6d3-577">Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ab6d3-578">Dostosuj limit w pliku *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="ab6d3-578">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ab6d3-579">To ustawienie dotyczy tylko usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-579">This setting only applies to IIS.</span></span> <span data-ttu-id="ab6d3-580">Zachowanie nie występuje domyślnie podczas hostowania w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ab6d3-581">Aby uzyskać więcej informacji, zobacz [limity żądań \< requestlimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ab6d3-582">Ograniczenia w module ASP.NET Core lub obecność modułu filtrowania żądań usług IIS mogą ograniczyć przekazywanie do 2 lub 4 GB.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ab6d3-583">Aby uzyskać więcej informacji, zobacz [nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ab6d3-584">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="ab6d3-584">Troubleshoot</span></span>

<span data-ttu-id="ab6d3-585">Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ab6d3-586">Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="ab6d3-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ab6d3-587">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="ab6d3-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ab6d3-588">Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usług IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ab6d3-589">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="ab6d3-589">Connection failure</span></span>

<span data-ttu-id="ab6d3-590">Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ab6d3-591">Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ab6d3-592">Limity połączeń klienta Kestrel mogą również wymagać korekty.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ab6d3-593">Wyjątek odwołania o wartości null z IFormFile</span><span class="sxs-lookup"><span data-stu-id="ab6d3-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ab6d3-594">Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ab6d3-595">Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ab6d3-596">Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ab6d3-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ab6d3-597">Strumień był zbyt długi</span><span class="sxs-lookup"><span data-stu-id="ab6d3-597">Stream was too long</span></span>

<span data-ttu-id="ab6d3-598">Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ab6d3-599">Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="ab6d3-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ab6d3-600">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="ab6d3-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="ab6d3-601">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ab6d3-601">Additional resources</span></span>

* [<span data-ttu-id="ab6d3-602">Opróżnianie żądań połączenia HTTP</span><span class="sxs-lookup"><span data-stu-id="ab6d3-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="ab6d3-603">Przekazywanie plików bez ograniczeń</span><span class="sxs-lookup"><span data-stu-id="ab6d3-603">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="ab6d3-604">Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Środki zaradcze</span><span class="sxs-lookup"><span data-stu-id="ab6d3-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="ab6d3-605">Wzorce projektowe chmury platformy Azure: wzorzec klucza portiera</span><span class="sxs-lookup"><span data-stu-id="ab6d3-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
