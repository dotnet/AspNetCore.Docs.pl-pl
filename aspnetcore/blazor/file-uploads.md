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
# <a name="aspnet-core-blazor-file-uploads"></a>ASP.NET Core Blazor operacje przekazywania plików

Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.

> [!WARNING]
> Zawsze przestrzegaj najlepszych rozwiązań w zakresie zabezpieczeń przekazywania plików. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile` cm6long

`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .

Domyślnie użytkownik wybiera pojedyncze pliki. Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie. Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.

Aby odczytać dane z pliku wybranego przez użytkownika:

* Wywołaj `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` plik i odczytaj go ze zwróconego strumienia. Aby uzyskać więcej informacji, zobacz sekcję [strumienie plików](#file-streams) .
* Wartość <xref:System.IO.Stream> zwracana przez `OpenReadStream` wymusza maksymalny rozmiar w bajtach `Stream` odczytywanych. Domyślnie pliki o rozmiarze większym niż 512 000 bajtów (500 KB) nie mogą zostać odczytane, zanim inne operacje odczytu spowodują wystąpienie wyjątku. Ten limit jest obecny, aby uniemożliwić deweloperom przypadkowe odczytywanie dużych plików w pamięci. `maxAllowedSize`Parametr on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` może służyć do określania większego rozmiaru, jeśli jest to wymagane.
* Należy unikać odczytywania przychodzącego strumienia plików bezpośrednio do pamięci. Na przykład nie Kopiuj bajtów pliku do <xref:System.IO.MemoryStream> lub Odczytaj jako tablicę bajtów. Te podejścia mogą powodować problemy z wydajnością i zabezpieczeniami, szczególnie w Blazor Server . Zamiast tego należy rozważyć kopiowanie bajtów plików do magazynu zewnętrznego, takiego jak obiekt BLOB lub plik na dysku.

Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.

Poniższy przykład ilustruje przekazywanie wielu plików w składniku. `InputFileChangeEventArgs.GetMultipleFiles` umożliwia odczytywanie wielu plików. Określ maksymalną liczbę plików, które należy odczytać, aby uniemożliwić złośliwemu użytkownikowi przekazanie większej liczby plików niż oczekiwano. `InputFileChangeEventArgs.File` zezwala na odczytywanie pierwszego i tylko pliku, jeśli przekazywanie plików nie obsługuje wielu plików.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> znajduje się w <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> przestrzeni nazw, co jest zazwyczaj jedną z przestrzeni nazw w pliku aplikacji `_Imports.razor` .

`Pages/UploadFiles.razor`:

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

`IBrowserFile` zwraca metadane [uwidocznione przez przeglądarkę](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako właściwości. Te metadane mogą być przydatne do wstępnej weryfikacji.

## <a name="upload-files-to-a-server"></a>Przekazywanie plików na serwer

Poniższy przykład ilustruje przekazywanie plików na serwer przy użyciu hostowanego Blazor WebAssembly rozwiązania.

> [!WARNING]
> Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.

Następująca `UploadResult` Klasa w **`Shared`** projekcie zachowuje wynik przekazanego pliku. Gdy przekazywanie pliku na serwer nie powiedzie się, kod błędu jest zwracany w programie `ErrorCode` na potrzeby wyświetlania użytkownikowi. Na serwerze są generowane bezpieczne przechowywane nazwy plików dla każdego pliku i zwracane do klienta w programie w `StoredFileName` celu wyświetlenia. Pliki są zmieniane między klientem a serwerem przy użyciu nazwy niebezpiecznej/niezaufanego pliku w programie `FileName` .

`UploadResult.cs`:

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

Następujący `UploadFiles` składnik w **`Client`** projekcie:

* Zezwala użytkownikom na przekazywanie plików z klienta programu.
* Wyświetla niezaufaną/niebezpieczną nazwę pliku dostarczoną przez klienta w interfejsie użytkownika, ponieważ automatycznie koduje Razor ciągi. **Nie ufaj nazwom plików dostarczanym przez klientów w innych scenariuszach.**

`Pages/UploadFiles.razor`:

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

Aby użyć poniższego kodu, należy utworzyć `Development/unsafe_uploads` folder w katalogu głównym **`Server`** projektu.

Poniższy `FilesaveController` kontroler w **`Server`** projekcie zapisuje przekazane pliki z klienta programu.

> [!WARNING]
> Przykład zapisuje pliki bez skanowania ich zawartości. W większości scenariuszy produkcyjnych interfejs API skanera chroniącego przed wirusami i złośliwym oprogramowaniem jest używany na plikach przed udostępnieniem ich do pobrania lub użycia przez inne systemy. Aby uzyskać więcej informacji na temat zagadnień związanych z zabezpieczeniami podczas przekazywania plików na serwer, zobacz <xref:mvc/models/file-uploads#security-considerations> .

`Controllers/FilesaveController.cs`:

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

## <a name="file-streams"></a>Strumienie plików

W programie Blazor WebAssembly dane plików są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.

W programie Blazor Server dane plików są przesyłane strumieniowo SignalR do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia. <xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> umożliwia skonfigurowanie parametrów przekazywania plików dla programu Blazor Server .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
