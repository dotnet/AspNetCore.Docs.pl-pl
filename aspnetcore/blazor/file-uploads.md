---
title: ASP.NET Core Blazor operacje przekazywania plików
author: guardrex
description: Dowiedz się, jak przekazywać pliki w Blazor składniku plik_wejściowy.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 02/18/2021
uid: blazor/file-uploads
ms.openlocfilehash: a31821f03efd39d774a4a3c61d027983a1783e2d
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395126"
---
# <a name="aspnet-core-blazor-file-uploads"></a><span data-ttu-id="07c59-103">ASP.NET Core Blazor operacje przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="07c59-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="07c59-104">Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="07c59-104">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="07c59-105">Zawsze przestrzegaj najlepszych rozwiązań w zakresie zabezpieczeń przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="07c59-105">Always follow file upload security best practices.</span></span> <span data-ttu-id="07c59-106">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="07c59-106">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="07c59-107">`InputFile` cm6long</span><span class="sxs-lookup"><span data-stu-id="07c59-107">`InputFile` component</span></span>

<span data-ttu-id="07c59-108">`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="07c59-108">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="07c59-109">Domyślnie użytkownik wybiera pojedyncze pliki.</span><span class="sxs-lookup"><span data-stu-id="07c59-109">By default, the user selects single files.</span></span> <span data-ttu-id="07c59-110">Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="07c59-110">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="07c59-111">Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.</span><span class="sxs-lookup"><span data-stu-id="07c59-111">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="07c59-112">Aby odczytać dane z pliku wybranego przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="07c59-112">To read data from a user-selected file:</span></span>

* <span data-ttu-id="07c59-113">Wywołaj `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` plik i odczytaj go ze zwróconego strumienia.</span><span class="sxs-lookup"><span data-stu-id="07c59-113">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="07c59-114">Aby uzyskać więcej informacji, zobacz sekcję [strumienie plików](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="07c59-114">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="07c59-115">Wartość <xref:System.IO.Stream> zwracana przez `OpenReadStream` wymusza maksymalny rozmiar w bajtach `Stream` odczytywanych.</span><span class="sxs-lookup"><span data-stu-id="07c59-115">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="07c59-116">Domyślnie pliki o rozmiarze większym niż 512 000 bajtów (500 KB) nie mogą zostać odczytane, zanim inne operacje odczytu spowodują wystąpienie wyjątku.</span><span class="sxs-lookup"><span data-stu-id="07c59-116">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="07c59-117">Ten limit jest obecny, aby uniemożliwić deweloperom przypadkowe odczytywanie dużych plików w pamięci.</span><span class="sxs-lookup"><span data-stu-id="07c59-117">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="07c59-118">`maxAllowedSize`Parametr on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` może służyć do określania większego rozmiaru, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="07c59-118">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="07c59-119">Należy unikać odczytywania przychodzącego strumienia plików bezpośrednio do pamięci.</span><span class="sxs-lookup"><span data-stu-id="07c59-119">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="07c59-120">Na przykład nie Kopiuj bajtów pliku do <xref:System.IO.MemoryStream> lub Odczytaj jako tablicę bajtów.</span><span class="sxs-lookup"><span data-stu-id="07c59-120">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="07c59-121">Te podejścia mogą powodować problemy z wydajnością i zabezpieczeniami, szczególnie w Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="07c59-121">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="07c59-122">Zamiast tego należy rozważyć kopiowanie bajtów plików do magazynu zewnętrznego, takiego jak obiekt BLOB lub plik na dysku.</span><span class="sxs-lookup"><span data-stu-id="07c59-122">Instead, consider copying file bytes to an external store, such as a blob or a file on disk.</span></span>

<span data-ttu-id="07c59-123">Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="07c59-123">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="07c59-124">Poniższy przykład ilustruje przekazywanie wielu plików w składniku.</span><span class="sxs-lookup"><span data-stu-id="07c59-124">The following example demonstrates multiple file upload in a component.</span></span> <span data-ttu-id="07c59-125">`InputFileChangeEventArgs.GetMultipleFiles` umożliwia odczytywanie wielu plików.</span><span class="sxs-lookup"><span data-stu-id="07c59-125">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="07c59-126">Określ maksymalną liczbę plików, które należy odczytać, aby uniemożliwić złośliwemu użytkownikowi przekazanie większej liczby plików niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="07c59-126">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="07c59-127">`InputFileChangeEventArgs.File` zezwala na odczytywanie pierwszego i tylko pliku, jeśli przekazywanie plików nie obsługuje wielu plików.</span><span class="sxs-lookup"><span data-stu-id="07c59-127">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload doesn't support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="07c59-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> znajduje się w <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> przestrzeni nazw, co jest zazwyczaj jedną z przestrzeni nazw w pliku aplikacji `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="07c59-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="07c59-129">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="07c59-129">`Pages/UploadFiles.razor`:</span></span>

```razor
@page "/upload-files"
@using System.IO

<h3>Upload Files</h3>

<p>
    <label>
        Max file size:
        <input type="number" @bind="maxFileSize" />
    </label>
</p>

<p>
    <label>
        Max allowed files:
        <input type="number" @bind="maxAllowedFiles" />
    </label>
</p>

<p>
    <label>
        Upload up to @maxAllowedFiles files of up to @maxFileSize bytes each:
        <InputFile OnChange="@LoadFiles" multiple />
    </label>
</p>

<p>@exceptionMessage</p>

@if (isLoading)
{
    <p>Loading...</p>
}

<ul>
    @foreach (var (file, content) in loadedFiles)
    {
        <li>
            <ul>
                <li>Name: @file.Name</li>
                <li>Last modified: @file.LastModified.ToString()</li>
                <li>Size (bytes): @file.Size</li>
                <li>Content type: @file.ContentType</li>
                <li>Content: @content</li>
            </ul>
        </li>
    }
</ul>

@code {
    private Dictionary<IBrowserFile, string> loadedFiles =
        new Dictionary<IBrowserFile, string>();
    private long maxFileSize = 1024 * 15;
    private int maxAllowedFiles = 3;
    private bool isLoading;
    string exceptionMessage;

    async Task LoadFiles(InputFileChangeEventArgs e)
    {
        isLoading = true;
        loadedFiles.Clear();
        exceptionMessage = string.Empty;

        try
        {
            foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
            {
                using var reader = 
                    new StreamReader(file.OpenReadStream(maxFileSize));

                loadedFiles.Add(file, await reader.ReadToEndAsync());
            }
        }
        catch (Exception ex)
        {
            exceptionMessage = ex.Message;
        }

        isLoading = false;
    }
}
```

<span data-ttu-id="07c59-130">`IBrowserFile` zwraca metadane [uwidocznione przez przeglądarkę](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako właściwości.</span><span class="sxs-lookup"><span data-stu-id="07c59-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="07c59-131">Te metadane mogą być przydatne do wstępnej weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="07c59-131">This metadata can be useful for preliminary validation.</span></span>

## <a name="upload-files-to-a-server"></a><span data-ttu-id="07c59-132">Przekazywanie plików na serwer</span><span class="sxs-lookup"><span data-stu-id="07c59-132">Upload files to a server</span></span>

<span data-ttu-id="07c59-133">Poniższy przykład ilustruje przekazywanie plików na serwer przy użyciu hostowanego Blazor WebAssembly rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="07c59-133">The following example demonstrates uploading files to a server using a hosted Blazor WebAssembly solution.</span></span>

> [!WARNING]
> <span data-ttu-id="07c59-134">Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer.</span><span class="sxs-lookup"><span data-stu-id="07c59-134">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="07c59-135">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="07c59-135">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="07c59-136">Następująca `UploadResult` Klasa w **`Shared`** projekcie zachowuje wynik przekazanego pliku.</span><span class="sxs-lookup"><span data-stu-id="07c59-136">The following `UploadResult` class in the **`Shared`** project maintains the result of an uploaded file.</span></span> <span data-ttu-id="07c59-137">Gdy przekazywanie pliku na serwer nie powiedzie się, kod błędu jest zwracany w programie `ErrorCode` na potrzeby wyświetlania użytkownikowi.</span><span class="sxs-lookup"><span data-stu-id="07c59-137">When a file fails to upload on the server, an error code is returned in `ErrorCode` for display to the user.</span></span> <span data-ttu-id="07c59-138">Na serwerze są generowane bezpieczne przechowywane nazwy plików dla każdego pliku i zwracane do klienta w programie w `StoredFileName` celu wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="07c59-138">A safe stored file name is generated on the server for each file and returned to the client in `StoredFileName` for display.</span></span> <span data-ttu-id="07c59-139">Pliki są zmieniane między klientem a serwerem przy użyciu nazwy niebezpiecznej/niezaufanego pliku w programie `FileName` .</span><span class="sxs-lookup"><span data-stu-id="07c59-139">Files are keyed between the client and server using the unsafe/untrusted file name in `FileName`.</span></span>

<span data-ttu-id="07c59-140">`UploadResult.cs`:</span><span class="sxs-lookup"><span data-stu-id="07c59-140">`UploadResult.cs`:</span></span>

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

<span data-ttu-id="07c59-141">Następujący `UploadFiles` składnik w **`Client`** projekcie:</span><span class="sxs-lookup"><span data-stu-id="07c59-141">The following `UploadFiles` component in the **`Client`** project:</span></span>

* <span data-ttu-id="07c59-142">Zezwala użytkownikom na przekazywanie plików z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="07c59-142">Permits users to upload files from the client.</span></span>
* <span data-ttu-id="07c59-143">Wyświetla niezaufaną/niebezpieczną nazwę pliku dostarczoną przez klienta w interfejsie użytkownika, ponieważ automatycznie koduje Razor ciągi.</span><span class="sxs-lookup"><span data-stu-id="07c59-143">Displays the untrusted/unsafe file name provided by the client in the UI because Razor automatically HTML-encodes strings.</span></span> <span data-ttu-id="07c59-144">**Nie ufaj nazwom plików dostarczanym przez klientów w innych scenariuszach.**</span><span class="sxs-lookup"><span data-stu-id="07c59-144">**Don't trust file names supplied by clients in other scenarios.**</span></span>

<span data-ttu-id="07c59-145">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="07c59-145">`Pages/UploadFiles.razor`:</span></span>

```razor
@page "/upload-files"
@using System.Linq
@using Microsoft.Extensions.Logging
@inject HttpClient Http
@inject ILogger<UploadFiles> logger

<h1>Upload Files</h1>

<p>
    <label>
        Upload up to @maxAllowedFiles files:
        <InputFile OnChange="@OnInputFileChange" multiple />
    </label>
</p>

@if (files.Count > 0)
{
    <div class="card">
        <div class="card-body">
            <ul>
                @foreach (var file in files)
                {
                    <li>
                        File: @file.Name
                        <br>
                        @if (FileUpload(uploadResults, file.Name, logger,
                            out var result))
                        {
                            <span>
                                Stored File Name: @result.StoredFileName
                            </span>
                        }
                        else
                        {
                            <span>
                                There was an error uploading the file
                                (Error: @result.ErrorCode).
                            </span>
                        }
                    </li>
                }
            </ul>
        </div>
    </div>
}

@code {
    private IList<File> files = new List<File>();
    private IList<UploadResult> uploadResults = new List<UploadResult>();
    private int maxAllowedFiles = 3;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        shouldRender = false;
        long maxFileSize = 1024 * 1024 * 15;
        var upload = false;

        using var content = new MultipartFormDataContent();

        foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
        {
            if (uploadResults.SingleOrDefault(
                f => f.FileName == file.Name) is null)
            {
                var fileContent = new StreamContent(file.OpenReadStream());

                files.Add(
                    new File()
                    {
                        Name = file.Name,
                    });

                if (file.Size < maxFileSize)
                {
                    content.Add(
                        content: fileContent,
                        name: "\"files\"",
                        fileName: file.Name);

                    upload = true;
                }
                else
                {
                    logger.LogInformation("{FileName} not uploaded", file.Name);

                    uploadResults.Add(
                        new UploadResult()
                        {
                            FileName = file.Name,
                            ErrorCode = 6,
                            Uploaded = false,
                        });
                }
            }
        }

        if (upload)
        {
            var response = await Http.PostAsync("/Filesave", content);

            var newUploadResults = await response.Content
                .ReadFromJsonAsync<IList<UploadResult>>();

            uploadResults = uploadResults.Concat(newUploadResults).ToList();
        }

        shouldRender = true;
    }

    private static bool FileUpload(IList<UploadResult> uploadResults,
        string fileName, ILogger<UploadFiles> logger, out UploadResult result)
    {
        result = uploadResults.SingleOrDefault(f => f.FileName == fileName);

        if (result is null)
        {
            logger.LogInformation("{FileName} not uploaded", fileName);
            result = new UploadResult();
            result.ErrorCode = 5;
        }

        return result.Uploaded;
    }

    private class File
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="07c59-146">Aby użyć poniższego kodu, należy utworzyć `Development/unsafe_uploads` folder w katalogu głównym **`Server`** projektu.</span><span class="sxs-lookup"><span data-stu-id="07c59-146">To use the following code, create a `Development/unsafe_uploads` folder at the root of the **`Server`** project.</span></span>

<span data-ttu-id="07c59-147">Poniższy `FilesaveController` kontroler w **`Server`** projekcie zapisuje przekazane pliki z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="07c59-147">The following `FilesaveController` controller in the **`Server`** project saves uploaded files from the client.</span></span>

> [!WARNING]
> <span data-ttu-id="07c59-148">Przykład zapisuje pliki bez skanowania ich zawartości.</span><span class="sxs-lookup"><span data-stu-id="07c59-148">The example saves files without scanning their contents.</span></span> <span data-ttu-id="07c59-149">W większości scenariuszy produkcyjnych interfejs API skanera chroniącego przed wirusami i złośliwym oprogramowaniem jest używany na plikach przed udostępnieniem ich do pobrania lub użycia przez inne systemy.</span><span class="sxs-lookup"><span data-stu-id="07c59-149">In most production scenarios, an anti-virus/anti-malware scanner API is used on files before making them available for download or for use by other systems.</span></span> <span data-ttu-id="07c59-150">Aby uzyskać więcej informacji na temat zagadnień związanych z zabezpieczeniami podczas przekazywania plików na serwer, zobacz <xref:mvc/models/file-uploads#security-considerations> .</span><span class="sxs-lookup"><span data-stu-id="07c59-150">For more information on security considerations when uploading files to a server, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="07c59-151">`Controllers/FilesaveController.cs`:</span><span class="sxs-lookup"><span data-stu-id="07c59-151">`Controllers/FilesaveController.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

[ApiController]
[Route("[controller]")]
public class FilesaveController : ControllerBase
{
    private readonly IWebHostEnvironment env;
    private readonly ILogger<FilesaveController> logger;

    public FilesaveController(IWebHostEnvironment env, 
        ILogger<FilesaveController> logger)
    {
        this.env = env;
        this.logger = logger;
    }

    [HttpPost]
    public async Task<ActionResult<IList<UploadResult>>> PostFile(
        [FromForm] IEnumerable<IFormFile> files)
    {
        var maxAllowedFiles = 3;
        long maxFileSize = 1024 * 1024 * 15;
        var filesProcessed = 0;
        var resourcePath = new Uri($"{Request.Scheme}://{Request.Host}/");
        IList<UploadResult> uploadResults = new List<UploadResult>();

        foreach (var file in files)
        {
            var uploadResult = new UploadResult();
            string trustedFileNameForFileStorage;
            var untrustedFileName = file.FileName;
            uploadResult.FileName = untrustedFileName;
            var trustedFileNameForDisplay = 
                WebUtility.HtmlEncode(untrustedFileName);

            if (filesProcessed < maxAllowedFiles)
            {
                if (file.Length == 0)
                {
                    logger.LogInformation("{FileName} length is 0", 
                        trustedFileNameForDisplay);
                    uploadResult.ErrorCode = 1;
                }
                else if (file.Length > maxFileSize)
                {
                    logger.LogInformation("{FileName} of {Length} bytes is " +
                        "larger than the limit of {Limit} bytes", 
                        trustedFileNameForDisplay, file.Length, maxFileSize);
                    uploadResult.ErrorCode = 2;
                }
                else
                {
                    try
                    {
                        trustedFileNameForFileStorage = Path.GetRandomFileName();
                        var path = Path.Combine(env.ContentRootPath, 
                            env.EnvironmentName, "unsafe_uploads", 
                            trustedFileNameForFileStorage);
                        using MemoryStream ms = new();
                        await file.CopyToAsync(ms);
                        await System.IO.File.WriteAllBytesAsync(path, ms.ToArray());
                        logger.LogInformation("{FileName} saved at {Path}", 
                            trustedFileNameForDisplay, path);
                        uploadResult.Uploaded = true;
                        uploadResult.StoredFileName = trustedFileNameForFileStorage;
                    }
                    catch (IOException ex)
                    {
                        logger.LogError("{FileName} error on upload: {Message}", 
                            trustedFileNameForDisplay, ex.Message);
                        uploadResult.ErrorCode = 3;
                    }
                }

                filesProcessed++;
            }
            else
            {
                logger.LogInformation("{FileName} not uploaded because the " +
                    "request exceeded the allowed {Count} of files", 
                    trustedFileNameForDisplay, maxAllowedFiles);
                uploadResult.ErrorCode = 4;
            }

            uploadResults.Add(uploadResult);
        }

        return new CreatedResult(resourcePath, uploadResults);
    }
}
```

## <a name="file-streams"></a><span data-ttu-id="07c59-152">Strumienie plików</span><span class="sxs-lookup"><span data-stu-id="07c59-152">File streams</span></span>

<span data-ttu-id="07c59-153">W programie Blazor WebAssembly dane plików są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="07c59-153">In Blazor WebAssembly, file data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="07c59-154">W programie Blazor Server dane plików są przesyłane strumieniowo SignalR do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="07c59-154">In Blazor Server, file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="07c59-155"><xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> umożliwia skonfigurowanie parametrów przekazywania plików dla programu Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="07c59-155"><xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07c59-156">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="07c59-156">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
