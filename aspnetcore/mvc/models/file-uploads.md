---
title: Przekazywanie plików w ASP.NET Core
author: rick-anderson
description: Jak używać powiązania modelu i przesyłania strumieniowego do przekazywania plików w ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661735"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="4f532-103">Przekazywanie plików w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f532-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="4f532-104">Przez [Steve Smith](https://ardalis.com/) i [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="4f532-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4f532-105">ASP.NET Core obsługuje przekazywanie jednego lub więcej plików przy użyciu powiązania modelu buforowanego dla mniejszych plików i niebuforowanego przesyłania strumieniowego dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="4f532-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f532-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4f532-107">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-107">Security considerations</span></span>

<span data-ttu-id="4f532-108">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="4f532-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="4f532-109">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="4f532-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="4f532-110">Wykonywanie [ataków typu "odmowa usługi".](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="4f532-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="4f532-111">Przesyłaj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="4f532-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="4f532-112">Narażaj sieci i serwery na inne sposoby.</span><span class="sxs-lookup"><span data-stu-id="4f532-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="4f532-113">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku są:</span><span class="sxs-lookup"><span data-stu-id="4f532-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="4f532-114">Przesyłaj pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="4f532-115">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przesłane pliki.</span><span class="sxs-lookup"><span data-stu-id="4f532-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="4f532-116">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="4f532-117">**Nie** utrwalić przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="4f532-118">Użyj bezpiecznej nazwy pliku określonej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="4f532-119">Nie używaj nazwy pliku podanej przez użytkownika ani niezaufanej nazwy pliku przekazanego. &dagger; Html koduje niezaufaną nazwę pliku podczas jej wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="4f532-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="4f532-120">Na przykład rejestrowanie nazwy pliku lub wyświetlania w interfejsie użytkownika (Razor automatycznie koduje dane wyjściowe).</span><span class="sxs-lookup"><span data-stu-id="4f532-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="4f532-121">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="4f532-122">Sprawdź, czy kontrole po stronie klienta są wykonywane na serwerze. &dagger; Kontrole po stronie klienta są łatwe do obejścia.</span><span class="sxs-lookup"><span data-stu-id="4f532-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="4f532-123">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="4f532-124">Ustaw maksymalny limit rozmiaru, aby zapobiec dużym przesyłaniu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="4f532-125">Jeśli pliki nie powinny być zastępowane przez przekazany plik o tej samej nazwie, przed przekazaniem pliku sprawdź nazwę pliku w bazie danych lub pamięci fizycznej.</span><span class="sxs-lookup"><span data-stu-id="4f532-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="4f532-126">**Uruchom skaner wirusów/złośliwego oprogramowania na przesłanych treściach przed zapisaniem pliku.**</span><span class="sxs-lookup"><span data-stu-id="4f532-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="4f532-127">&dagger;Przykładowa aplikacja demonstruje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="4f532-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-128">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="4f532-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="4f532-129">Całkowicie przejmij kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="4f532-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="4f532-130">Przeciążenie systemu w wyniku awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="4f532-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="4f532-131">Narażaj dane użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="4f532-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="4f532-132">Stosowanie graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="4f532-133">Aby uzyskać informacje na temat zmniejszania powierzchni ataku podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="4f532-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="4f532-134">Nieograniczone przekazywanie plików</span><span class="sxs-lookup"><span data-stu-id="4f532-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="4f532-135">Zabezpieczenia platformy Azure: upewnij się, że podczas akceptowania plików od użytkowników są dostępne odpowiednie formanty</span><span class="sxs-lookup"><span data-stu-id="4f532-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="4f532-136">Aby uzyskać więcej informacji na temat wdrażania środków zabezpieczeń, w tym przykłady z przykładowej aplikacji, zobacz sekcję [Sprawdzania poprawności.](#validation)</span><span class="sxs-lookup"><span data-stu-id="4f532-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="4f532-137">Scenariusze magazynowania</span><span class="sxs-lookup"><span data-stu-id="4f532-137">Storage scenarios</span></span>

<span data-ttu-id="4f532-138">Typowe opcje przechowywania plików obejmują:</span><span class="sxs-lookup"><span data-stu-id="4f532-138">Common storage options for files include:</span></span>

* <span data-ttu-id="4f532-139">baza danych</span><span class="sxs-lookup"><span data-stu-id="4f532-139">Database</span></span>

  * <span data-ttu-id="4f532-140">W przypadku przekazywania małych plików baza danych jest często szybsza niż opcje magazynu fizycznego (system plików lub udział sieciowy).</span><span class="sxs-lookup"><span data-stu-id="4f532-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="4f532-141">Baza danych jest często wygodniejsza niż opcje magazynu fizycznego, ponieważ pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczać zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="4f532-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="4f532-142">Baza danych jest potencjalnie tańsze niż przy użyciu usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="4f532-143">Pamięć fizyczna (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="4f532-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="4f532-144">W przypadku przesyłania dużych plików:</span><span class="sxs-lookup"><span data-stu-id="4f532-144">For large file uploads:</span></span>
    * <span data-ttu-id="4f532-145">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="4f532-146">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="4f532-147">Magazyn fizyczny jest potencjalnie tańszy niż korzystanie z usługi przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="4f532-148">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="4f532-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="4f532-149">**Nigdy nie udzielaj uprawnień do wykonywania.**</span><span class="sxs-lookup"><span data-stu-id="4f532-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="4f532-150">Usługa przechowywania danych (na przykład [usługa Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="4f532-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="4f532-151">Usługi zwykle oferują lepszą skalowalność i odporność w przypadku rozwiązań lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="4f532-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="4f532-152">Usługi są potencjalnie niższe koszty w scenariuszach infrastruktury magazynu dużych.</span><span class="sxs-lookup"><span data-stu-id="4f532-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="4f532-153">Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą platformy .NET.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="4f532-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="4f532-154">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="4f532-154">File upload scenarios</span></span>

<span data-ttu-id="4f532-155">Dwa ogólne podejścia do przekazywania plików są buforowanie i przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="4f532-156">**Buforowanie**</span><span class="sxs-lookup"><span data-stu-id="4f532-156">**Buffering**</span></span>

<span data-ttu-id="4f532-157">Cały plik jest odczytywany <xref:Microsoft.AspNetCore.Http.IFormFile>do , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="4f532-158">Zasoby (dysk, pamięć) używane przez przesyłane pliki zależą od liczby i rozmiaru równoczesnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="4f532-159">Jeśli aplikacja próbuje buforować zbyt wiele przekazywania, witryna ulega awarii, gdy zabraknie miejsca na dysku lub.</span><span class="sxs-lookup"><span data-stu-id="4f532-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="4f532-160">Jeśli rozmiar lub częstotliwość przekazywania plików jest wyczerpujące zasoby aplikacji, użyj przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="4f532-161">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="4f532-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="4f532-162">Buforowanie małych plików jest omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="4f532-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="4f532-163">Pamięć fizyczna</span><span class="sxs-lookup"><span data-stu-id="4f532-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="4f532-164">baza danych</span><span class="sxs-lookup"><span data-stu-id="4f532-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="4f532-165">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="4f532-165">**Streaming**</span></span>

<span data-ttu-id="4f532-166">Plik jest odbierany z żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="4f532-167">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="4f532-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="4f532-168">Przesyłanie strumieniowe zmniejsza zapotrzebowanie na pamięć lub miejsce na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="4f532-169">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [Przekaż duże pliki z przesyłanie strumieniowe.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="4f532-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="4f532-170">Przekazywanie małych plików z powiązaniem modelu buforowanego do magazynu fizycznego</span><span class="sxs-lookup"><span data-stu-id="4f532-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="4f532-171">Aby przesłać małe pliki, użyj formularza wieloczęściowego lub skonstruować żądanie POST za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4f532-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="4f532-172">W poniższym przykładzie pokazano użycie formularza Razor Pages do przesłania pojedynczego pliku *(Pages/BufferedSingleFileUploadPhysical.cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="4f532-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="4f532-173">Poniższy przykład jest analogiczny do poprzedniego przykładu, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="4f532-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="4f532-174">JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="4f532-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="4f532-175">Nie ma sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="4f532-175">There's no validation.</span></span>

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

<span data-ttu-id="4f532-176">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania,](https://caniuse.com/#feat=fetch)użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="4f532-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="4f532-177">Użyj polyfill pobierania (na przykład [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="4f532-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="4f532-178">Użyj witryny `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="4f532-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="4f532-179">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-179">For example:</span></span>

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

<span data-ttu-id="4f532-180">Aby obsługiwać przesyłanie plików, formularze HTML muszą`enctype`określać typ kodowania ( ) programu `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="4f532-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="4f532-181">Dla `files` elementu wejściowego do obsługi przekazywania wielu plików podaj `multiple` atrybut na `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="4f532-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="4f532-182">Dostęp do poszczególnych plików przesłanych do serwera <xref:Microsoft.AspNetCore.Http.IFormFile>można uzyskać za pośrednictwem [funkcji Powiązanie modelu](xref:mvc/models/model-binding) za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="4f532-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="4f532-183">Przykładowa aplikacja pokazuje wiele buforowanych przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="4f532-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="4f532-184">**Nie** należy `FileName` używać <xref:Microsoft.AspNetCore.Http.IFormFile> właściwości innych niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="4f532-185">Podczas wyświetlania lub rejestrowania kod html koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="4f532-186">Osoba atakująca może podać złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="4f532-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="4f532-187">Wnioski powinny:</span><span class="sxs-lookup"><span data-stu-id="4f532-187">Applications should:</span></span>
>
> * <span data-ttu-id="4f532-188">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="4f532-189">Zapisz zakodowaną w formacie HTML nazwę pliku usuniętą ze ścieżki dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="4f532-190">Wygeneruj nową losową nazwę pliku do przechowywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="4f532-191">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="4f532-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="4f532-192">Przedstawione do tej pory przykłady nie uwzględniają względów bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="4f532-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="4f532-193">Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="4f532-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4f532-194">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4f532-195">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-195">Validation</span></span>](#validation)

<span data-ttu-id="4f532-196">Podczas przesyłania plików przy <xref:Microsoft.AspNetCore.Http.IFormFile>użyciu powiązania modelu i , metoda akcji może zaakceptować:</span><span class="sxs-lookup"><span data-stu-id="4f532-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="4f532-197">Pojedynczy <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="4f532-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="4f532-198">Dowolna z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="4f532-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="4f532-199">[Listy](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="4f532-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="4f532-200">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="4f532-200">Binding matches form files by name.</span></span> <span data-ttu-id="4f532-201">Na przykład wartość `name` HTML `<input type="file" name="formFile">` w musi odpowiadać c#`FormFile`parametr/właściwość związana ( ).</span><span class="sxs-lookup"><span data-stu-id="4f532-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="4f532-202">Aby uzyskać więcej informacji, zobacz [Match name valuee do nazwy parametru](#match-name-attribute-value-to-parameter-name-of-post-method) sekcji metody POST.</span><span class="sxs-lookup"><span data-stu-id="4f532-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="4f532-203">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-203">The following example:</span></span>

* <span data-ttu-id="4f532-204">Pętle za pośrednictwem jednego lub więcej przesłanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="4f532-205">Używa [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) do zwrócenia pełnej ścieżki dla pliku, w tym nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="4f532-206">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="4f532-207">Zwraca całkowitą liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="4f532-208">Służy `Path.GetRandomFileName` do generowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="4f532-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="4f532-209">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="4f532-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="4f532-210">Ścieżka przekazana do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="4f532-211">Jeśli nazwa pliku nie jest <xref:System.UnauthorizedAccessException> podana, jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="4f532-212">Pliki przekazywane przy <xref:Microsoft.AspNetCore.Http.IFormFile> użyciu tej techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="4f532-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="4f532-213">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna <xref:System.IO.Stream>jako plik .</span><span class="sxs-lookup"><span data-stu-id="4f532-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="4f532-214">Oprócz lokalnego systemu plików pliki można zapisywać w udziale sieciowym lub w usłudze przechowywania plików, takiej jak [magazyn obiektów Blob platformy Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="4f532-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="4f532-215">Inny przykład, który pętli na wiele plików do przekazywania i używa bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> zgłasza, jeśli więcej niż 65,535 pliki są tworzone bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="4f532-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="4f532-217">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="4f532-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="4f532-218">Aby uzyskać więcej informacji na temat tego limitu w systemie operacyjnym Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="4f532-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="4f532-219">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="4f532-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="4f532-220">Przekazywanie małych plików z powiązaniem modelu buforowanego do bazy danych</span><span class="sxs-lookup"><span data-stu-id="4f532-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="4f532-221">Aby przechowywać dane pliku binarnego w <xref:System.Byte> bazie danych przy użyciu programu Entity [Framework,](/ef/core/index)należy zdefiniować właściwość tablicy w jednostce:</span><span class="sxs-lookup"><span data-stu-id="4f532-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="4f532-222">Określ właściwość modelu strony dla <xref:Microsoft.AspNetCore.Http.IFormFile>klasy, która zawiera:</span><span class="sxs-lookup"><span data-stu-id="4f532-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="4f532-223"><xref:Microsoft.AspNetCore.Http.IFormFile>może służyć bezpośrednio jako parametr metody akcji lub jako właściwość modelu powiązanego.</span><span class="sxs-lookup"><span data-stu-id="4f532-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="4f532-224">W poprzednim przykładzie używa właściwości modelu powiązanego.</span><span class="sxs-lookup"><span data-stu-id="4f532-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="4f532-225">Jest `FileUpload` używany w formularzu Strony Razor:</span><span class="sxs-lookup"><span data-stu-id="4f532-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="4f532-226">Gdy formularz jest posted do serwera, <xref:Microsoft.AspNetCore.Http.IFormFile> skopiować do strumienia i zapisać go jako tablicy bajtów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="4f532-227">W poniższym `_dbContext` przykładzie przechowuje kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="4f532-228">W poprzednim przykładzie jest podobny do scenariusza zademonstrowanego w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="4f532-229">*Strony/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4f532-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="4f532-230">*Strony/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4f532-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-231">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych baz danych, ponieważ może to niekorzystnie wpłynąć na wydajność.</span><span class="sxs-lookup"><span data-stu-id="4f532-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="4f532-232">Nie polegaj na właściwości `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności ani nie ufaj jej.</span><span class="sxs-lookup"><span data-stu-id="4f532-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="4f532-233">Właściwość `FileName` powinna być używana tylko do celów wyświetlania i tylko po kodowaniu HTML.</span><span class="sxs-lookup"><span data-stu-id="4f532-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="4f532-234">Podane przykłady nie uwzględniają kwestii bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="4f532-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="4f532-235">Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="4f532-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4f532-236">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4f532-237">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="4f532-238">Przesyłanie dużych plików za pomocą przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="4f532-238">Upload large files with streaming</span></span>

<span data-ttu-id="4f532-239">W poniższym przykładzie pokazano, jak używać języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4f532-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="4f532-240">Token antyforgery pliku jest generowany przy użyciu atrybutu filtru niestandardowego i przekazywany do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="4f532-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="4f532-241">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="4f532-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="4f532-242">W ramach akcji zawartość formularza jest odczytywana za pomocą `MultipartReader`, który odczytuje każdą osobę, `MultipartSection`przetwarzania pliku lub przechowywania zawartości w stosownych przypadkach.</span><span class="sxs-lookup"><span data-stu-id="4f532-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="4f532-243">Po odczytaniu sekcji wieloczęściowych akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="4f532-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="4f532-244">Początkowa odpowiedź strony wczytuje formularz i zapisuje token antyforgery w pliku cookie (za pomocą atrybutu). `GenerateAntiforgeryTokenCookieAttribute`</span><span class="sxs-lookup"><span data-stu-id="4f532-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="4f532-245">Atrybut używa wbudowanej [obsługi antyforgery](xref:security/anti-request-forgery) ASP.NET Core do ustawiania pliku cookie z tokenem żądania:</span><span class="sxs-lookup"><span data-stu-id="4f532-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="4f532-246">Jest `DisableFormValueModelBindingAttribute` używany do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="4f532-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="4f532-247">W przykładowej `GenerateAntiforgeryTokenCookieAttribute` aplikacji `DisableFormValueModelBindingAttribute` i są stosowane jako filtry `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` modeli aplikacji strony i przy użyciu [konwencji Razor Pages:](xref:razor-pages/razor-pages-conventions)</span><span class="sxs-lookup"><span data-stu-id="4f532-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="4f532-248">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza, nie wiążą się (kwerenda, trasa i nagłówek nadal działają).</span><span class="sxs-lookup"><span data-stu-id="4f532-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="4f532-249">Metoda akcji działa bezpośrednio `Request` z właściwością.</span><span class="sxs-lookup"><span data-stu-id="4f532-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="4f532-250">A `MultipartReader` służy do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="4f532-251">Dane klucza/wartości są `KeyValueAccumulator`przechowywane w pliku .</span><span class="sxs-lookup"><span data-stu-id="4f532-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="4f532-252">Po odczytywaniu sekcji wieloczęściowych `KeyValueAccumulator` zawartość są używane do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="4f532-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="4f532-253">Pełna `StreamingController.UploadDatabase` metoda przesyłania strumieniowego do bazy danych za pomocą ef core:</span><span class="sxs-lookup"><span data-stu-id="4f532-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="4f532-254">`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="4f532-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="4f532-255">Pełna `StreamingController.UploadPhysical` metoda przesyłania strumieniowego do fizycznej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="4f532-256">W przykładowej aplikacji sprawdzanie poprawności `FileHelpers.ProcessStreamedFile`jest obsługiwane przez program .</span><span class="sxs-lookup"><span data-stu-id="4f532-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="4f532-257">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-257">Validation</span></span>

<span data-ttu-id="4f532-258">`FileHelpers` Klasa przykładowej aplikacji pokazuje kilka kontroli dla <xref:Microsoft.AspNetCore.Http.IFormFile> plików buforowanych i przesyłanych strumieniowo.</span><span class="sxs-lookup"><span data-stu-id="4f532-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="4f532-259">Aby <xref:Microsoft.AspNetCore.Http.IFormFile> uzyskać przetwarzanie buforowanych przekazywania plików `ProcessFormFile` w przykładowej aplikacji, zobacz metodę w pliku *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="4f532-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="4f532-260">Aby przetworzyć pliki `ProcessStreamedFile` przesyłane strumieniowo, zobacz metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-261">Metody przetwarzania sprawdzania poprawności zademonstrowane w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="4f532-262">W większości scenariuszy produkcyjnych interfejs API skanera wirusów/złośliwego oprogramowania jest używany w pliku przed udostępnieniem pliku użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="4f532-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="4f532-263">Chociaż przykład tematu zawiera roboczy przykład technik sprawdzania `FileHelpers` poprawności, nie implementuj klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="4f532-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="4f532-264">W pełni zrozumieć implementację.</span><span class="sxs-lookup"><span data-stu-id="4f532-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="4f532-265">Zmodyfikuj implementację odpowiednio dla środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="4f532-266">**Nigdy nie należy bez krytycznie implementować kodu zabezpieczeń w aplikacji bez spełnienia tych wymagań.**</span><span class="sxs-lookup"><span data-stu-id="4f532-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="4f532-267">Sprawdzanie poprawności zawartości</span><span class="sxs-lookup"><span data-stu-id="4f532-267">Content validation</span></span>

<span data-ttu-id="4f532-268">**Użyj interfejsu API skanowania wirusów/złośliwego oprogramowania innej firmy w przesłanych treściach.**</span><span class="sxs-lookup"><span data-stu-id="4f532-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="4f532-269">Skanowanie plików jest wymagające w zasobach serwera w scenariuszach o dużej objętości.</span><span class="sxs-lookup"><span data-stu-id="4f532-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="4f532-270">Jeśli wydajność przetwarzania żądań jest zmniejszona z powodu skanowania plików, należy rozważyć odciążenie pracy skanowania do [usługi w tle,](xref:fundamentals/host/hosted-services)ewentualnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="4f532-271">Zazwyczaj przekazywane pliki są przechowywane w obszarze poddanym kwarantannie, dopóki skaner antywirusowy w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="4f532-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="4f532-272">Po przejściu pliku plik jest przenoszony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="4f532-273">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="4f532-274">Przy użyciu takiego podejścia, aplikacji i serwera aplikacji pozostają skoncentrowane na odpowiadaniu na żądania.</span><span class="sxs-lookup"><span data-stu-id="4f532-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="4f532-275">Sprawdzanie poprawności rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-275">File extension validation</span></span>

<span data-ttu-id="4f532-276">Rozszerzenie przekazanego pliku należy sprawdzić na liście dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="4f532-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="4f532-277">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="4f532-278">Sprawdzanie poprawności podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-278">File signature validation</span></span>

<span data-ttu-id="4f532-279">Podpis pliku jest określany przez kilka pierwszych bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="4f532-280">Bajty te mogą służyć do wskazania, czy rozszerzenie pasuje do zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="4f532-281">Przykładowa aplikacja sprawdza podpisy plików dla kilku typowych typów plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="4f532-282">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany względem pliku:</span><span class="sxs-lookup"><span data-stu-id="4f532-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="4f532-283">Aby uzyskać dodatkowe podpisy plików, zobacz [baza danych podpisów plików](https://www.filesignatures.net/) i oficjalne specyfikacje plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="4f532-284">Zabezpieczenia nazwy pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-284">File name security</span></span>

<span data-ttu-id="4f532-285">Nigdy nie używaj nazwy pliku dostarczonego przez klienta do zapisywania pliku w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="4f532-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="4f532-286">Utwórz bezpieczną nazwę pliku przy użyciu [path.getrandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) aby utworzyć pełną ścieżkę (w tym nazwę pliku) do tymczasowego przechowywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="4f532-287">Razor automatycznie koduje wartości właściwości do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="4f532-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="4f532-288">Poniższy kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="4f532-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="4f532-289">Poza Razor, <xref:System.Net.WebUtility.HtmlEncode*> zawsze plik zawartości nazwy z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="4f532-290">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik jest zastępowany plikiem o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="4f532-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="4f532-291">Podaj dodatkową logikę, aby spełnić wymagania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="4f532-292">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="4f532-292">Size validation</span></span>

<span data-ttu-id="4f532-293">Ogranicz rozmiar przesłanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="4f532-294">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wskazany w bajtach).</span><span class="sxs-lookup"><span data-stu-id="4f532-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="4f532-295">Limit jest dostarczany za pośrednictwem [konfiguracji](xref:fundamentals/configuration/index) z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="4f532-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="4f532-296">`FileSizeLimit` Wstrzykuje `PageModel` się do klas:</span><span class="sxs-lookup"><span data-stu-id="4f532-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="4f532-297">Gdy rozmiar pliku przekracza limit, plik jest odrzucany:</span><span class="sxs-lookup"><span data-stu-id="4f532-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="4f532-298">Dopasuj wartość atrybutu nazwa do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="4f532-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="4f532-299">W formularzach innych niż Razor, które publikują dane formularza lub używają javascript bezpośrednio, nazwa określona `FormData` w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4f532-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="4f532-300">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="4f532-300">In the following example:</span></span>

* <span data-ttu-id="4f532-301">Podczas korzystania `<input>` z `name` elementu, atrybut jest `battlePlans`ustawiony na wartość:</span><span class="sxs-lookup"><span data-stu-id="4f532-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="4f532-302">Podczas `FormData` korzystania w javascript, nazwa jest `battlePlans`ustawiona na wartość:</span><span class="sxs-lookup"><span data-stu-id="4f532-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="4f532-303">Użyj pasującej nazwy parametru metody`battlePlans`C# ( ):</span><span class="sxs-lookup"><span data-stu-id="4f532-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="4f532-304">Dla metody obsługi stron Razor o nazwie: `Upload`</span><span class="sxs-lookup"><span data-stu-id="4f532-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="4f532-305">Dla metody akcji kontrolera MVC POST:</span><span class="sxs-lookup"><span data-stu-id="4f532-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="4f532-306">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="4f532-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="4f532-307">Granica długości obiektu wieloczęściowego</span><span class="sxs-lookup"><span data-stu-id="4f532-307">Multipart body length limit</span></span>

<span data-ttu-id="4f532-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdego obiektu wieloczęściowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="4f532-309">Sekcje formularza, które przekraczają <xref:System.IO.InvalidDataException> ten limit, są rzutowane podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="4f532-310">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="4f532-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="4f532-311">Dostosuj limit za <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocą `Startup.ConfigureServices`ustawienia w :</span><span class="sxs-lookup"><span data-stu-id="4f532-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="4f532-313">W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:</span><span class="sxs-lookup"><span data-stu-id="4f532-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-314">W aplikacji Razor Pages lub aplikacji MVC zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="4f532-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="4f532-315">Rozmiar ciała pustułka maksymalnego żądania</span><span class="sxs-lookup"><span data-stu-id="4f532-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="4f532-316">W przypadku aplikacji obsługiwanych przez Kestrel domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4f532-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="4f532-317">Dostosuj limit za pomocą opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4f532-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4f532-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="4f532-319">W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:</span><span class="sxs-lookup"><span data-stu-id="4f532-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-320">W aplikacji stron Razor lub aplikacji MVC zastosuj filtr do klasy obsługi strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="4f532-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="4f532-321">Może `RequestSizeLimitAttribute` być również stosowany [`@attribute`](xref:mvc/views/razor#attribute) przy użyciu dyrektywy Razor:</span><span class="sxs-lookup"><span data-stu-id="4f532-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="4f532-322">Inne limity pustułki</span><span class="sxs-lookup"><span data-stu-id="4f532-322">Other Kestrel limits</span></span>

<span data-ttu-id="4f532-323">Inne limity Kestrel mogą mieć zastosowanie do aplikacji obsługiwanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4f532-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="4f532-324">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="4f532-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="4f532-325">Wskaźniki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="4f532-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="4f532-326">Limit długości zawartości w uiścić w uiścić zawartość</span><span class="sxs-lookup"><span data-stu-id="4f532-326">IIS content length limit</span></span>

<span data-ttu-id="4f532-327">Domyślny limit`maxAllowedContentLength`żądań ( ) wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4f532-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="4f532-328">Dostosuj limit w pliku *web.config:*</span><span class="sxs-lookup"><span data-stu-id="4f532-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="4f532-329">To ustawienie dotyczy tylko iIS.</span><span class="sxs-lookup"><span data-stu-id="4f532-329">This setting only applies to IIS.</span></span> <span data-ttu-id="4f532-330">Zachowanie nie występuje domyślnie podczas hostingu na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4f532-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="4f532-331">Aby uzyskać więcej informacji, zobacz [Żądanie limitów \<RequestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="4f532-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="4f532-332">Ograniczenia w ASP.NET modułu rdzenia lub obecność modułu filtrowania żądań IIS mogą ograniczać przesyłanie do 2 lub 4 GB.</span><span class="sxs-lookup"><span data-stu-id="4f532-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="4f532-333">Aby uzyskać więcej informacji, zobacz [Nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="4f532-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4f532-334">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="4f532-334">Troubleshoot</span></span>

<span data-ttu-id="4f532-335">Poniżej znajduje się kilka typowych problemów napotkanych podczas pracy z przesyłaniem plików i ich możliwych rozwiązań.</span><span class="sxs-lookup"><span data-stu-id="4f532-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="4f532-336">Nie znaleziono błędu podczas wdrażania na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="4f532-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="4f532-337">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowany numer zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="4f532-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="4f532-338">Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usługi IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="4f532-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="4f532-339">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="4f532-339">Connection failure</span></span>

<span data-ttu-id="4f532-340">Błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazują, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4f532-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="4f532-341">Aby uzyskać więcej informacji, zobacz [Kestrel maksymalny rozmiar treści żądania](#kestrel-maximum-request-body-size) sekcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="4f532-342">Limity połączeń klienta pustułki mogą również wymagać regulacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="4f532-343">Wyjątek odwołania zerowego z plikiem IFormFile</span><span class="sxs-lookup"><span data-stu-id="4f532-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="4f532-344">Jeśli kontroler akceptuje przesłane pliki <xref:Microsoft.AspNetCore.Http.IFormFile> przy użyciu, ale wartość jest `null`, `enctype` upewnij `multipart/form-data`się, że formularz HTML określa wartość .</span><span class="sxs-lookup"><span data-stu-id="4f532-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="4f532-345">Jeśli ten atrybut nie jest `<form>` ustawiony na elemencie, przekazywanie <xref:Microsoft.AspNetCore.Http.IFormFile> pliku `null`nie występuje i wszystkie powiązane argumenty są .</span><span class="sxs-lookup"><span data-stu-id="4f532-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="4f532-346">Upewnij się również, że [nazewnictwo przesyłania w danych formularza jest zgodne z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="4f532-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="4f532-347">Strumień był za długi</span><span class="sxs-lookup"><span data-stu-id="4f532-347">Stream was too long</span></span>

<span data-ttu-id="4f532-348">Przykłady w tym temacie <xref:System.IO.MemoryStream> polegać na przechowywać zawartość przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="4f532-349">Limit rozmiaru a `MemoryStream` `int.MaxValue`to .</span><span class="sxs-lookup"><span data-stu-id="4f532-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="4f532-350">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku większej niż 50 MB, użyj `MemoryStream` alternatywnego podejścia, które nie polega na jednym do przechowywania zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4f532-351">ASP.NET Core obsługuje przekazywanie jednego lub więcej plików przy użyciu powiązania modelu buforowanego dla mniejszych plików i niebuforowanego przesyłania strumieniowego dla większych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="4f532-352">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f532-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4f532-353">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-353">Security considerations</span></span>

<span data-ttu-id="4f532-354">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="4f532-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="4f532-355">Osoby atakujące mogą próbować:</span><span class="sxs-lookup"><span data-stu-id="4f532-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="4f532-356">Wykonywanie [ataków typu "odmowa usługi".](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="4f532-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="4f532-357">Przesyłaj wirusy lub złośliwe oprogramowanie.</span><span class="sxs-lookup"><span data-stu-id="4f532-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="4f532-358">Narażaj sieci i serwery na inne sposoby.</span><span class="sxs-lookup"><span data-stu-id="4f532-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="4f532-359">Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku są:</span><span class="sxs-lookup"><span data-stu-id="4f532-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="4f532-360">Przesyłaj pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="4f532-361">Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przesłane pliki.</span><span class="sxs-lookup"><span data-stu-id="4f532-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="4f532-362">Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="4f532-363">**Nie** utrwalić przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="4f532-364">Użyj bezpiecznej nazwy pliku określonej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="4f532-365">Nie używaj nazwy pliku podanej przez użytkownika ani niezaufanej nazwy pliku przekazanego. &dagger; Html koduje niezaufaną nazwę pliku podczas jej wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="4f532-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="4f532-366">Na przykład rejestrowanie nazwy pliku lub wyświetlania w interfejsie użytkownika (Razor automatycznie koduje dane wyjściowe).</span><span class="sxs-lookup"><span data-stu-id="4f532-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="4f532-367">Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="4f532-368">Sprawdź, czy kontrole po stronie klienta są wykonywane na serwerze. &dagger; Kontrole po stronie klienta są łatwe do obejścia.</span><span class="sxs-lookup"><span data-stu-id="4f532-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="4f532-369">Sprawdź rozmiar przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="4f532-370">Ustaw maksymalny limit rozmiaru, aby zapobiec dużym przesyłaniu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4f532-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="4f532-371">Jeśli pliki nie powinny być zastępowane przez przekazany plik o tej samej nazwie, przed przekazaniem pliku sprawdź nazwę pliku w bazie danych lub pamięci fizycznej.</span><span class="sxs-lookup"><span data-stu-id="4f532-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="4f532-372">**Uruchom skaner wirusów/złośliwego oprogramowania na przesłanych treściach przed zapisaniem pliku.**</span><span class="sxs-lookup"><span data-stu-id="4f532-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="4f532-373">&dagger;Przykładowa aplikacja demonstruje podejście, które spełnia kryteria.</span><span class="sxs-lookup"><span data-stu-id="4f532-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-374">Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:</span><span class="sxs-lookup"><span data-stu-id="4f532-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="4f532-375">Całkowicie przejmij kontrolę nad systemem.</span><span class="sxs-lookup"><span data-stu-id="4f532-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="4f532-376">Przeciążenie systemu w wyniku awarii systemu.</span><span class="sxs-lookup"><span data-stu-id="4f532-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="4f532-377">Narażaj dane użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="4f532-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="4f532-378">Stosowanie graffiti do publicznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="4f532-379">Aby uzyskać informacje na temat zmniejszania powierzchni ataku podczas akceptowania plików od użytkowników, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="4f532-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="4f532-380">Nieograniczone przekazywanie plików</span><span class="sxs-lookup"><span data-stu-id="4f532-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="4f532-381">Zabezpieczenia platformy Azure: upewnij się, że podczas akceptowania plików od użytkowników są dostępne odpowiednie formanty</span><span class="sxs-lookup"><span data-stu-id="4f532-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="4f532-382">Aby uzyskać więcej informacji na temat wdrażania środków zabezpieczeń, w tym przykłady z przykładowej aplikacji, zobacz sekcję [Sprawdzania poprawności.](#validation)</span><span class="sxs-lookup"><span data-stu-id="4f532-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="4f532-383">Scenariusze magazynowania</span><span class="sxs-lookup"><span data-stu-id="4f532-383">Storage scenarios</span></span>

<span data-ttu-id="4f532-384">Typowe opcje przechowywania plików obejmują:</span><span class="sxs-lookup"><span data-stu-id="4f532-384">Common storage options for files include:</span></span>

* <span data-ttu-id="4f532-385">baza danych</span><span class="sxs-lookup"><span data-stu-id="4f532-385">Database</span></span>

  * <span data-ttu-id="4f532-386">W przypadku przekazywania małych plików baza danych jest często szybsza niż opcje magazynu fizycznego (system plików lub udział sieciowy).</span><span class="sxs-lookup"><span data-stu-id="4f532-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="4f532-387">Baza danych jest często wygodniejsza niż opcje magazynu fizycznego, ponieważ pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczać zawartość pliku (na przykład obraz awatara).</span><span class="sxs-lookup"><span data-stu-id="4f532-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="4f532-388">Baza danych jest potencjalnie tańsze niż przy użyciu usługi magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="4f532-389">Pamięć fizyczna (system plików lub udział sieciowy)</span><span class="sxs-lookup"><span data-stu-id="4f532-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="4f532-390">W przypadku przesyłania dużych plików:</span><span class="sxs-lookup"><span data-stu-id="4f532-390">For large file uploads:</span></span>
    * <span data-ttu-id="4f532-391">Limity bazy danych mogą ograniczać rozmiar przekazywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="4f532-392">Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="4f532-393">Magazyn fizyczny jest potencjalnie tańszy niż korzystanie z usługi przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="4f532-394">Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="4f532-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="4f532-395">**Nigdy nie udzielaj uprawnień do wykonywania.**</span><span class="sxs-lookup"><span data-stu-id="4f532-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="4f532-396">Usługa przechowywania danych (na przykład [usługa Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="4f532-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="4f532-397">Usługi zwykle oferują lepszą skalowalność i odporność w przypadku rozwiązań lokalnych, które zwykle podlegają pojedynczym punktom awarii.</span><span class="sxs-lookup"><span data-stu-id="4f532-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="4f532-398">Usługi są potencjalnie niższe koszty w scenariuszach infrastruktury magazynu dużych.</span><span class="sxs-lookup"><span data-stu-id="4f532-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="4f532-399">Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą platformy .NET.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="4f532-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="4f532-400">W temacie <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>przedstawiono <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ale może <xref:System.IO.FileStream> służyć do zapisywania <xref:System.IO.Stream>do magazynu obiektów blob podczas pracy z .</span><span class="sxs-lookup"><span data-stu-id="4f532-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="4f532-401">Scenariusze przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="4f532-401">File upload scenarios</span></span>

<span data-ttu-id="4f532-402">Dwa ogólne podejścia do przekazywania plików są buforowanie i przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="4f532-403">**Buforowanie**</span><span class="sxs-lookup"><span data-stu-id="4f532-403">**Buffering**</span></span>

<span data-ttu-id="4f532-404">Cały plik jest odczytywany <xref:Microsoft.AspNetCore.Http.IFormFile>do , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="4f532-405">Zasoby (dysk, pamięć) używane przez przesyłane pliki zależą od liczby i rozmiaru równoczesnych przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="4f532-406">Jeśli aplikacja próbuje buforować zbyt wiele przekazywania, witryna ulega awarii, gdy zabraknie miejsca na dysku lub.</span><span class="sxs-lookup"><span data-stu-id="4f532-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="4f532-407">Jeśli rozmiar lub częstotliwość przekazywania plików jest wyczerpujące zasoby aplikacji, użyj przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="4f532-408">Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.</span><span class="sxs-lookup"><span data-stu-id="4f532-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="4f532-409">Buforowanie małych plików jest omówione w następujących sekcjach tego tematu:</span><span class="sxs-lookup"><span data-stu-id="4f532-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="4f532-410">Pamięć fizyczna</span><span class="sxs-lookup"><span data-stu-id="4f532-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="4f532-411">baza danych</span><span class="sxs-lookup"><span data-stu-id="4f532-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="4f532-412">**Przesyłanie strumieniowe**</span><span class="sxs-lookup"><span data-stu-id="4f532-412">**Streaming**</span></span>

<span data-ttu-id="4f532-413">Plik jest odbierany z żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="4f532-414">Przesyłanie strumieniowe nie poprawia znacząco wydajności.</span><span class="sxs-lookup"><span data-stu-id="4f532-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="4f532-415">Przesyłanie strumieniowe zmniejsza zapotrzebowanie na pamięć lub miejsce na dysku podczas przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="4f532-416">Przesyłanie strumieniowe dużych plików jest omówione w sekcji [Przekaż duże pliki z przesyłanie strumieniowe.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="4f532-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="4f532-417">Przekazywanie małych plików z powiązaniem modelu buforowanego do magazynu fizycznego</span><span class="sxs-lookup"><span data-stu-id="4f532-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="4f532-418">Aby przesłać małe pliki, użyj formularza wieloczęściowego lub skonstruować żądanie POST za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4f532-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="4f532-419">W poniższym przykładzie pokazano użycie formularza Razor Pages do przesłania pojedynczego pliku *(Pages/BufferedSingleFileUploadPhysical.cshtml* w przykładowej aplikacji):</span><span class="sxs-lookup"><span data-stu-id="4f532-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="4f532-420">Poniższy przykład jest analogiczny do poprzedniego przykładu, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="4f532-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="4f532-421">JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)służy do przesyłania danych formularza.</span><span class="sxs-lookup"><span data-stu-id="4f532-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="4f532-422">Nie ma sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="4f532-422">There's no validation.</span></span>

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

<span data-ttu-id="4f532-423">Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania,](https://caniuse.com/#feat=fetch)użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="4f532-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="4f532-424">Użyj polyfill pobierania (na przykład [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="4f532-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="4f532-425">Użyj witryny `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="4f532-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="4f532-426">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-426">For example:</span></span>

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

<span data-ttu-id="4f532-427">Aby obsługiwać przesyłanie plików, formularze HTML muszą`enctype`określać typ kodowania ( ) programu `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="4f532-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="4f532-428">Dla `files` elementu wejściowego do obsługi przekazywania wielu plików podaj `multiple` atrybut na `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="4f532-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="4f532-429">Dostęp do poszczególnych plików przesłanych do serwera <xref:Microsoft.AspNetCore.Http.IFormFile>można uzyskać za pośrednictwem [funkcji Powiązanie modelu](xref:mvc/models/model-binding) za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="4f532-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="4f532-430">Przykładowa aplikacja pokazuje wiele buforowanych przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.</span><span class="sxs-lookup"><span data-stu-id="4f532-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="4f532-431">**Nie** należy `FileName` używać <xref:Microsoft.AspNetCore.Http.IFormFile> właściwości innych niż do wyświetlania i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="4f532-432">Podczas wyświetlania lub rejestrowania kod html koduje nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="4f532-433">Osoba atakująca może podać złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="4f532-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="4f532-434">Wnioski powinny:</span><span class="sxs-lookup"><span data-stu-id="4f532-434">Applications should:</span></span>
>
> * <span data-ttu-id="4f532-435">Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="4f532-436">Zapisz zakodowaną w formacie HTML nazwę pliku usuniętą ze ścieżki dla interfejsu użytkownika lub rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="4f532-437">Wygeneruj nową losową nazwę pliku do przechowywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="4f532-438">Poniższy kod usuwa ścieżkę z nazwy pliku:</span><span class="sxs-lookup"><span data-stu-id="4f532-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="4f532-439">Przedstawione do tej pory przykłady nie uwzględniają względów bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="4f532-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="4f532-440">Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="4f532-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4f532-441">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4f532-442">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-442">Validation</span></span>](#validation)

<span data-ttu-id="4f532-443">Podczas przesyłania plików przy <xref:Microsoft.AspNetCore.Http.IFormFile>użyciu powiązania modelu i , metoda akcji może zaakceptować:</span><span class="sxs-lookup"><span data-stu-id="4f532-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="4f532-444">Pojedynczy <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="4f532-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="4f532-445">Dowolna z następujących kolekcji, które reprezentują kilka plików:</span><span class="sxs-lookup"><span data-stu-id="4f532-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="4f532-446">[Listy](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="4f532-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="4f532-447">Powiązanie dopasowuje pliki formularza według nazwy.</span><span class="sxs-lookup"><span data-stu-id="4f532-447">Binding matches form files by name.</span></span> <span data-ttu-id="4f532-448">Na przykład wartość `name` HTML `<input type="file" name="formFile">` w musi odpowiadać c#`FormFile`parametr/właściwość związana ( ).</span><span class="sxs-lookup"><span data-stu-id="4f532-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="4f532-449">Aby uzyskać więcej informacji, zobacz [Match name valuee do nazwy parametru](#match-name-attribute-value-to-parameter-name-of-post-method) sekcji metody POST.</span><span class="sxs-lookup"><span data-stu-id="4f532-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="4f532-450">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-450">The following example:</span></span>

* <span data-ttu-id="4f532-451">Pętle za pośrednictwem jednego lub więcej przesłanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="4f532-452">Używa [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) do zwrócenia pełnej ścieżki dla pliku, w tym nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="4f532-453">Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="4f532-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="4f532-454">Zwraca całkowitą liczbę i rozmiar przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="4f532-455">Służy `Path.GetRandomFileName` do generowania nazwy pliku bez ścieżki.</span><span class="sxs-lookup"><span data-stu-id="4f532-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="4f532-456">W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="4f532-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="4f532-457">Ścieżka przekazana do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="4f532-458">Jeśli nazwa pliku nie jest <xref:System.UnauthorizedAccessException> podana, jest generowany w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="4f532-459">Pliki przekazywane przy <xref:Microsoft.AspNetCore.Http.IFormFile> użyciu tej techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem.</span><span class="sxs-lookup"><span data-stu-id="4f532-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="4f532-460">Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna <xref:System.IO.Stream>jako plik .</span><span class="sxs-lookup"><span data-stu-id="4f532-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="4f532-461">Oprócz lokalnego systemu plików pliki można zapisywać w udziale sieciowym lub w usłudze przechowywania plików, takiej jak [magazyn obiektów Blob platformy Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="4f532-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="4f532-462">Inny przykład, który pętli na wiele plików do przekazywania i używa bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> zgłasza, jeśli więcej niż 65,535 pliki są tworzone bez usuwania poprzednich plików tymczasowych.</span><span class="sxs-lookup"><span data-stu-id="4f532-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="4f532-464">Limit 65 535 plików jest limitem dla serwera.</span><span class="sxs-lookup"><span data-stu-id="4f532-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="4f532-465">Aby uzyskać więcej informacji na temat tego limitu w systemie operacyjnym Windows, zobacz uwagi w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="4f532-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="4f532-466">GetTempFileNameA, funkcja</span><span class="sxs-lookup"><span data-stu-id="4f532-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="4f532-467">Przekazywanie małych plików z powiązaniem modelu buforowanego do bazy danych</span><span class="sxs-lookup"><span data-stu-id="4f532-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="4f532-468">Aby przechowywać dane pliku binarnego w <xref:System.Byte> bazie danych przy użyciu programu Entity [Framework,](/ef/core/index)należy zdefiniować właściwość tablicy w jednostce:</span><span class="sxs-lookup"><span data-stu-id="4f532-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="4f532-469">Określ właściwość modelu strony dla <xref:Microsoft.AspNetCore.Http.IFormFile>klasy, która zawiera:</span><span class="sxs-lookup"><span data-stu-id="4f532-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="4f532-470"><xref:Microsoft.AspNetCore.Http.IFormFile>może służyć bezpośrednio jako parametr metody akcji lub jako właściwość modelu powiązanego.</span><span class="sxs-lookup"><span data-stu-id="4f532-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="4f532-471">W poprzednim przykładzie używa właściwości modelu powiązanego.</span><span class="sxs-lookup"><span data-stu-id="4f532-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="4f532-472">Jest `FileUpload` używany w formularzu Strony Razor:</span><span class="sxs-lookup"><span data-stu-id="4f532-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="4f532-473">Gdy formularz jest posted do serwera, <xref:Microsoft.AspNetCore.Http.IFormFile> skopiować do strumienia i zapisać go jako tablicy bajtów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4f532-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="4f532-474">W poniższym `_dbContext` przykładzie przechowuje kontekst bazy danych aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="4f532-475">W poprzednim przykładzie jest podobny do scenariusza zademonstrowanego w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="4f532-476">*Strony/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4f532-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="4f532-477">*Strony/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4f532-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-478">Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych baz danych, ponieważ może to niekorzystnie wpłynąć na wydajność.</span><span class="sxs-lookup"><span data-stu-id="4f532-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="4f532-479">Nie polegaj na właściwości `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności ani nie ufaj jej.</span><span class="sxs-lookup"><span data-stu-id="4f532-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="4f532-480">Właściwość `FileName` powinna być używana tylko do celów wyświetlania i tylko po kodowaniu HTML.</span><span class="sxs-lookup"><span data-stu-id="4f532-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="4f532-481">Podane przykłady nie uwzględniają kwestii bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="4f532-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="4f532-482">Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="4f532-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4f532-483">Zagadnienia dotyczące bezpieczeństwa</span><span class="sxs-lookup"><span data-stu-id="4f532-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4f532-484">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="4f532-485">Przesyłanie dużych plików za pomocą przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="4f532-485">Upload large files with streaming</span></span>

<span data-ttu-id="4f532-486">W poniższym przykładzie pokazano, jak używać języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4f532-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="4f532-487">Token antyforgery pliku jest generowany przy użyciu atrybutu filtru niestandardowego i przekazywany do nagłówków HTTP klienta zamiast w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="4f532-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="4f532-488">Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="4f532-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="4f532-489">W ramach akcji zawartość formularza jest odczytywana za pomocą `MultipartReader`, który odczytuje każdą osobę, `MultipartSection`przetwarzania pliku lub przechowywania zawartości w stosownych przypadkach.</span><span class="sxs-lookup"><span data-stu-id="4f532-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="4f532-490">Po odczytaniu sekcji wieloczęściowych akcja wykonuje własne powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="4f532-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="4f532-491">Początkowa odpowiedź strony wczytuje formularz i zapisuje token antyforgery w pliku cookie (za pomocą atrybutu). `GenerateAntiforgeryTokenCookieAttribute`</span><span class="sxs-lookup"><span data-stu-id="4f532-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="4f532-492">Atrybut używa wbudowanej [obsługi antyforgery](xref:security/anti-request-forgery) ASP.NET Core do ustawiania pliku cookie z tokenem żądania:</span><span class="sxs-lookup"><span data-stu-id="4f532-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="4f532-493">Jest `DisableFormValueModelBindingAttribute` używany do wyłączania powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="4f532-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="4f532-494">W przykładowej `GenerateAntiforgeryTokenCookieAttribute` aplikacji `DisableFormValueModelBindingAttribute` i są stosowane jako filtry `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` modeli aplikacji strony i przy użyciu [konwencji Razor Pages:](xref:razor-pages/razor-pages-conventions)</span><span class="sxs-lookup"><span data-stu-id="4f532-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="4f532-495">Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza, nie wiążą się (kwerenda, trasa i nagłówek nadal działają).</span><span class="sxs-lookup"><span data-stu-id="4f532-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="4f532-496">Metoda akcji działa bezpośrednio `Request` z właściwością.</span><span class="sxs-lookup"><span data-stu-id="4f532-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="4f532-497">A `MultipartReader` służy do odczytywania każdej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="4f532-498">Dane klucza/wartości są `KeyValueAccumulator`przechowywane w pliku .</span><span class="sxs-lookup"><span data-stu-id="4f532-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="4f532-499">Po odczytywaniu sekcji wieloczęściowych `KeyValueAccumulator` zawartość są używane do powiązania danych formularza z typem modelu.</span><span class="sxs-lookup"><span data-stu-id="4f532-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="4f532-500">Pełna `StreamingController.UploadDatabase` metoda przesyłania strumieniowego do bazy danych za pomocą ef core:</span><span class="sxs-lookup"><span data-stu-id="4f532-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="4f532-501">`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="4f532-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="4f532-502">Pełna `StreamingController.UploadPhysical` metoda przesyłania strumieniowego do fizycznej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="4f532-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="4f532-503">W przykładowej aplikacji sprawdzanie poprawności `FileHelpers.ProcessStreamedFile`jest obsługiwane przez program .</span><span class="sxs-lookup"><span data-stu-id="4f532-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="4f532-504">Walidacja</span><span class="sxs-lookup"><span data-stu-id="4f532-504">Validation</span></span>

<span data-ttu-id="4f532-505">`FileHelpers` Klasa przykładowej aplikacji pokazuje kilka kontroli dla <xref:Microsoft.AspNetCore.Http.IFormFile> plików buforowanych i przesyłanych strumieniowo.</span><span class="sxs-lookup"><span data-stu-id="4f532-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="4f532-506">Aby <xref:Microsoft.AspNetCore.Http.IFormFile> uzyskać przetwarzanie buforowanych przekazywania plików `ProcessFormFile` w przykładowej aplikacji, zobacz metodę w pliku *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="4f532-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="4f532-507">Aby przetworzyć pliki `ProcessStreamedFile` przesyłane strumieniowo, zobacz metodę w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="4f532-508">Metody przetwarzania sprawdzania poprawności zademonstrowane w przykładowej aplikacji nie skanują zawartości przekazanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="4f532-509">W większości scenariuszy produkcyjnych interfejs API skanera wirusów/złośliwego oprogramowania jest używany w pliku przed udostępnieniem pliku użytkownikom lub innym systemom.</span><span class="sxs-lookup"><span data-stu-id="4f532-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="4f532-510">Chociaż przykład tematu zawiera roboczy przykład technik sprawdzania `FileHelpers` poprawności, nie implementuj klasy w aplikacji produkcyjnej, chyba że:</span><span class="sxs-lookup"><span data-stu-id="4f532-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="4f532-511">W pełni zrozumieć implementację.</span><span class="sxs-lookup"><span data-stu-id="4f532-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="4f532-512">Zmodyfikuj implementację odpowiednio dla środowiska i specyfikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="4f532-513">**Nigdy nie należy bez krytycznie implementować kodu zabezpieczeń w aplikacji bez spełnienia tych wymagań.**</span><span class="sxs-lookup"><span data-stu-id="4f532-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="4f532-514">Sprawdzanie poprawności zawartości</span><span class="sxs-lookup"><span data-stu-id="4f532-514">Content validation</span></span>

<span data-ttu-id="4f532-515">**Użyj interfejsu API skanowania wirusów/złośliwego oprogramowania innej firmy w przesłanych treściach.**</span><span class="sxs-lookup"><span data-stu-id="4f532-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="4f532-516">Skanowanie plików jest wymagające w zasobach serwera w scenariuszach o dużej objętości.</span><span class="sxs-lookup"><span data-stu-id="4f532-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="4f532-517">Jeśli wydajność przetwarzania żądań jest zmniejszona z powodu skanowania plików, należy rozważyć odciążenie pracy skanowania do [usługi w tle,](xref:fundamentals/host/hosted-services)ewentualnie usługi uruchomionej na serwerze innym niż serwer aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="4f532-518">Zazwyczaj przekazywane pliki są przechowywane w obszarze poddanym kwarantannie, dopóki skaner antywirusowy w tle nie sprawdzi ich.</span><span class="sxs-lookup"><span data-stu-id="4f532-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="4f532-519">Po przejściu pliku plik jest przenoszony do normalnej lokalizacji przechowywania plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="4f532-520">Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje stan skanowania pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="4f532-521">Przy użyciu takiego podejścia, aplikacji i serwera aplikacji pozostają skoncentrowane na odpowiadaniu na żądania.</span><span class="sxs-lookup"><span data-stu-id="4f532-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="4f532-522">Sprawdzanie poprawności rozszerzenia pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-522">File extension validation</span></span>

<span data-ttu-id="4f532-523">Rozszerzenie przekazanego pliku należy sprawdzić na liście dozwolonych rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="4f532-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="4f532-524">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4f532-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="4f532-525">Sprawdzanie poprawności podpisu pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-525">File signature validation</span></span>

<span data-ttu-id="4f532-526">Podpis pliku jest określany przez kilka pierwszych bajtów na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="4f532-527">Bajty te mogą służyć do wskazania, czy rozszerzenie pasuje do zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="4f532-528">Przykładowa aplikacja sprawdza podpisy plików dla kilku typowych typów plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="4f532-529">W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany względem pliku:</span><span class="sxs-lookup"><span data-stu-id="4f532-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="4f532-530">Aby uzyskać dodatkowe podpisy plików, zobacz [baza danych podpisów plików](https://www.filesignatures.net/) i oficjalne specyfikacje plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="4f532-531">Zabezpieczenia nazwy pliku</span><span class="sxs-lookup"><span data-stu-id="4f532-531">File name security</span></span>

<span data-ttu-id="4f532-532">Nigdy nie używaj nazwy pliku dostarczonego przez klienta do zapisywania pliku w magazynie fizycznym.</span><span class="sxs-lookup"><span data-stu-id="4f532-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="4f532-533">Utwórz bezpieczną nazwę pliku przy użyciu [path.getrandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) aby utworzyć pełną ścieżkę (w tym nazwę pliku) do tymczasowego przechowywania.</span><span class="sxs-lookup"><span data-stu-id="4f532-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="4f532-534">Razor automatycznie koduje wartości właściwości do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="4f532-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="4f532-535">Poniższy kod jest bezpieczny w użyciu:</span><span class="sxs-lookup"><span data-stu-id="4f532-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="4f532-536">Poza Razor, <xref:System.Net.WebUtility.HtmlEncode*> zawsze plik zawartości nazwy z żądania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f532-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="4f532-537">Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik jest zastępowany plikiem o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="4f532-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="4f532-538">Podaj dodatkową logikę, aby spełnić wymagania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="4f532-539">Sprawdzanie poprawności rozmiaru</span><span class="sxs-lookup"><span data-stu-id="4f532-539">Size validation</span></span>

<span data-ttu-id="4f532-540">Ogranicz rozmiar przesłanych plików.</span><span class="sxs-lookup"><span data-stu-id="4f532-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="4f532-541">W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wskazany w bajtach).</span><span class="sxs-lookup"><span data-stu-id="4f532-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="4f532-542">Limit jest dostarczany za pośrednictwem [konfiguracji](xref:fundamentals/configuration/index) z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="4f532-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="4f532-543">`FileSizeLimit` Wstrzykuje `PageModel` się do klas:</span><span class="sxs-lookup"><span data-stu-id="4f532-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="4f532-544">Gdy rozmiar pliku przekracza limit, plik jest odrzucany:</span><span class="sxs-lookup"><span data-stu-id="4f532-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="4f532-545">Dopasuj wartość atrybutu nazwa do nazwy parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="4f532-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="4f532-546">W formularzach innych niż Razor, które publikują dane formularza lub używają javascript bezpośrednio, nazwa określona `FormData` w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4f532-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="4f532-547">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="4f532-547">In the following example:</span></span>

* <span data-ttu-id="4f532-548">Podczas korzystania `<input>` z `name` elementu, atrybut jest `battlePlans`ustawiony na wartość:</span><span class="sxs-lookup"><span data-stu-id="4f532-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="4f532-549">Podczas `FormData` korzystania w javascript, nazwa jest `battlePlans`ustawiona na wartość:</span><span class="sxs-lookup"><span data-stu-id="4f532-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="4f532-550">Użyj pasującej nazwy parametru metody`battlePlans`C# ( ):</span><span class="sxs-lookup"><span data-stu-id="4f532-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="4f532-551">Dla metody obsługi stron Razor o nazwie: `Upload`</span><span class="sxs-lookup"><span data-stu-id="4f532-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="4f532-552">Dla metody akcji kontrolera MVC POST:</span><span class="sxs-lookup"><span data-stu-id="4f532-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="4f532-553">Konfiguracja serwera i aplikacji</span><span class="sxs-lookup"><span data-stu-id="4f532-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="4f532-554">Granica długości obiektu wieloczęściowego</span><span class="sxs-lookup"><span data-stu-id="4f532-554">Multipart body length limit</span></span>

<span data-ttu-id="4f532-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdego obiektu wieloczęściowego.</span><span class="sxs-lookup"><span data-stu-id="4f532-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="4f532-556">Sekcje formularza, które przekraczają <xref:System.IO.InvalidDataException> ten limit, są rzutowane podczas analizowania.</span><span class="sxs-lookup"><span data-stu-id="4f532-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="4f532-557">Wartość domyślna to 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="4f532-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="4f532-558">Dostosuj limit za <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocą `Startup.ConfigureServices`ustawienia w :</span><span class="sxs-lookup"><span data-stu-id="4f532-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="4f532-560">W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:</span><span class="sxs-lookup"><span data-stu-id="4f532-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-561">W aplikacji Razor Pages lub aplikacji MVC zastosuj filtr do modelu strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="4f532-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="4f532-562">Rozmiar ciała pustułka maksymalnego żądania</span><span class="sxs-lookup"><span data-stu-id="4f532-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="4f532-563">W przypadku aplikacji obsługiwanych przez Kestrel domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4f532-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="4f532-564">Dostosuj limit za pomocą opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4f532-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="4f532-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="4f532-566">W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:</span><span class="sxs-lookup"><span data-stu-id="4f532-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4f532-567">W aplikacji stron Razor lub aplikacji MVC zastosuj filtr do klasy obsługi strony lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="4f532-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="4f532-568">Inne limity pustułki</span><span class="sxs-lookup"><span data-stu-id="4f532-568">Other Kestrel limits</span></span>

<span data-ttu-id="4f532-569">Inne limity Kestrel mogą mieć zastosowanie do aplikacji obsługiwanych przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4f532-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="4f532-570">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="4f532-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="4f532-571">Wskaźniki danych żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="4f532-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="4f532-572">Limit długości zawartości w uiścić w uiścić zawartość</span><span class="sxs-lookup"><span data-stu-id="4f532-572">IIS content length limit</span></span>

<span data-ttu-id="4f532-573">Domyślny limit`maxAllowedContentLength`żądań ( ) wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4f532-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="4f532-574">Dostosuj limit w pliku *web.config:*</span><span class="sxs-lookup"><span data-stu-id="4f532-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="4f532-575">To ustawienie dotyczy tylko iIS.</span><span class="sxs-lookup"><span data-stu-id="4f532-575">This setting only applies to IIS.</span></span> <span data-ttu-id="4f532-576">Zachowanie nie występuje domyślnie podczas hostingu na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4f532-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="4f532-577">Aby uzyskać więcej informacji, zobacz [Żądanie limitów \<RequestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="4f532-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="4f532-578">Ograniczenia w ASP.NET modułu rdzenia lub obecność modułu filtrowania żądań IIS mogą ograniczać przesyłanie do 2 lub 4 GB.</span><span class="sxs-lookup"><span data-stu-id="4f532-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="4f532-579">Aby uzyskać więcej informacji, zobacz [Nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="4f532-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4f532-580">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="4f532-580">Troubleshoot</span></span>

<span data-ttu-id="4f532-581">Poniżej znajduje się kilka typowych problemów napotkanych podczas pracy z przesyłaniem plików i ich możliwych rozwiązań.</span><span class="sxs-lookup"><span data-stu-id="4f532-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="4f532-582">Nie znaleziono błędu podczas wdrażania na serwerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="4f532-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="4f532-583">Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowany numer zawartości serwera:</span><span class="sxs-lookup"><span data-stu-id="4f532-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="4f532-584">Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usługi IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="4f532-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="4f532-585">Błąd połączenia</span><span class="sxs-lookup"><span data-stu-id="4f532-585">Connection failure</span></span>

<span data-ttu-id="4f532-586">Błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazują, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4f532-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="4f532-587">Aby uzyskać więcej informacji, zobacz [Kestrel maksymalny rozmiar treści żądania](#kestrel-maximum-request-body-size) sekcji.</span><span class="sxs-lookup"><span data-stu-id="4f532-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="4f532-588">Limity połączeń klienta pustułki mogą również wymagać regulacji.</span><span class="sxs-lookup"><span data-stu-id="4f532-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="4f532-589">Wyjątek odwołania zerowego z plikiem IFormFile</span><span class="sxs-lookup"><span data-stu-id="4f532-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="4f532-590">Jeśli kontroler akceptuje przesłane pliki <xref:Microsoft.AspNetCore.Http.IFormFile> przy użyciu, ale wartość jest `null`, `enctype` upewnij `multipart/form-data`się, że formularz HTML określa wartość .</span><span class="sxs-lookup"><span data-stu-id="4f532-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="4f532-591">Jeśli ten atrybut nie jest `<form>` ustawiony na elemencie, przekazywanie <xref:Microsoft.AspNetCore.Http.IFormFile> pliku `null`nie występuje i wszystkie powiązane argumenty są .</span><span class="sxs-lookup"><span data-stu-id="4f532-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="4f532-592">Upewnij się również, że [nazewnictwo przesyłania w danych formularza jest zgodne z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="4f532-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="4f532-593">Strumień był za długi</span><span class="sxs-lookup"><span data-stu-id="4f532-593">Stream was too long</span></span>

<span data-ttu-id="4f532-594">Przykłady w tym temacie <xref:System.IO.MemoryStream> polegać na przechowywać zawartość przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="4f532-595">Limit rozmiaru a `MemoryStream` `int.MaxValue`to .</span><span class="sxs-lookup"><span data-stu-id="4f532-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="4f532-596">Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku większej niż 50 MB, użyj `MemoryStream` alternatywnego podejścia, które nie polega na jednym do przechowywania zawartości przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="4f532-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="4f532-597">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="4f532-597">Additional resources</span></span>

* [<span data-ttu-id="4f532-598">Nieograniczone przekazywanie plików</span><span class="sxs-lookup"><span data-stu-id="4f532-598">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="4f532-599">Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Czynniki</span><span class="sxs-lookup"><span data-stu-id="4f532-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="4f532-600">Wzorce projektowania chmury azure: wzorzec klucza usługi valet</span><span class="sxs-lookup"><span data-stu-id="4f532-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
