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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: ca49564136e030fdaf86eefac56146fcb79f7bad
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673955"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="f5d65-103">ASP.NET Core Blazor operacje przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="f5d65-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="f5d65-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="f5d65-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="f5d65-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5d65-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f5d65-106">Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="f5d65-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="f5d65-107">Zawsze przestrzegaj najlepszych rozwiązań w zakresie zabezpieczeń przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="f5d65-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="f5d65-108">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="f5d65-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="f5d65-109">`InputFile` cm6long</span><span class="sxs-lookup"><span data-stu-id="f5d65-109">`InputFile` component</span></span>

<span data-ttu-id="f5d65-110">`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="f5d65-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="f5d65-111">Domyślnie użytkownik wybiera pojedyncze pliki.</span><span class="sxs-lookup"><span data-stu-id="f5d65-111">By default, the user selects single files.</span></span> <span data-ttu-id="f5d65-112">Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="f5d65-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="f5d65-113">Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.</span><span class="sxs-lookup"><span data-stu-id="f5d65-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="f5d65-114">Aby odczytać dane z pliku wybranego przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="f5d65-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="f5d65-115">Wywołaj `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` plik i odczytaj go ze zwróconego strumienia.</span><span class="sxs-lookup"><span data-stu-id="f5d65-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="f5d65-116">Aby uzyskać więcej informacji, zobacz sekcję [strumienie plików](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="f5d65-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="f5d65-117">Wartość <xref:System.IO.Stream> zwracana przez `OpenReadStream` wymusza maksymalny rozmiar w bajtach `Stream` odczytywanych.</span><span class="sxs-lookup"><span data-stu-id="f5d65-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="f5d65-118">Domyślnie pliki o rozmiarze większym niż 512 000 bajtów (500 KB) nie mogą zostać odczytane, zanim inne operacje odczytu spowodują wystąpienie wyjątku.</span><span class="sxs-lookup"><span data-stu-id="f5d65-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="f5d65-119">Ten limit jest obecny, aby uniemożliwić deweloperom przypadkowe odczytywanie dużych plików w pamięci.</span><span class="sxs-lookup"><span data-stu-id="f5d65-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="f5d65-120">`maxAllowedSize`Parametr on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` może służyć do określania większego rozmiaru, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="f5d65-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="f5d65-121">Należy unikać odczytywania przychodzącego strumienia plików bezpośrednio do pamięci.</span><span class="sxs-lookup"><span data-stu-id="f5d65-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="f5d65-122">Na przykład nie Kopiuj bajtów pliku do <xref:System.IO.MemoryStream> lub Odczytaj jako tablicę bajtów.</span><span class="sxs-lookup"><span data-stu-id="f5d65-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="f5d65-123">Te podejścia mogą powodować problemy z wydajnością i zabezpieczeniami, szczególnie w Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f5d65-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="f5d65-124">Zamiast tego należy rozważyć kopiowanie bajtów plików do magazynu zewnętrznego, takiego jak obiekt BLOB lub plik na dysku.</span><span class="sxs-lookup"><span data-stu-id="f5d65-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="f5d65-125">Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f5d65-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="f5d65-126">Poniższy przykład ilustruje przekazywanie wielu plików obrazu w składniku.</span><span class="sxs-lookup"><span data-stu-id="f5d65-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="f5d65-127">`InputFileChangeEventArgs.GetMultipleFiles` umożliwia odczytywanie wielu plików.</span><span class="sxs-lookup"><span data-stu-id="f5d65-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="f5d65-128">Określ maksymalną liczbę plików, które należy odczytać, aby uniemożliwić złośliwemu użytkownikowi przekazanie większej liczby plików niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="f5d65-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="f5d65-129">`InputFileChangeEventArgs.File` zezwala na odczytywanie pierwszego i tylko pliku, jeśli przekazywanie plików nie obsługuje wielu plików.</span><span class="sxs-lookup"><span data-stu-id="f5d65-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="f5d65-130">`IBrowserFile` zwraca metadane [uwidocznione przez przeglądarkę](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako właściwości.</span><span class="sxs-lookup"><span data-stu-id="f5d65-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="f5d65-131">Te metadane mogą być przydatne do wstępnej weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="f5d65-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="f5d65-132">Na przykład zobacz [ `FileUpload.razor` i `FilePreview.razor` przykładowe składniki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="f5d65-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="f5d65-133">Strumienie plików</span><span class="sxs-lookup"><span data-stu-id="f5d65-133">File streams</span></span>

<span data-ttu-id="f5d65-134">W Blazor WebAssembly aplikacji dane są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="f5d65-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="f5d65-135">W Blazor Server aplikacji dane plików są przesyłane strumieniowo przez SignalR połączenie do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="f5d65-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="f5d65-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) umożliwia skonfigurowanie parametrów przekazywania plików dla programu Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f5d65-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5d65-137">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f5d65-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
