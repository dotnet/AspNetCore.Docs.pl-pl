---
title: 'ASP.NET Core :::no-loc(Blazor)::: operacje przekazywania plików'
author: guardrex
description: 'Dowiedz się, jak przekazywać pliki w :::no-loc(Blazor)::: składniku plik_wejściowy.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056130"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="4640c-103">ASP.NET Core :::no-loc(Blazor)::: operacje przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="4640c-103">ASP.NET Core :::no-loc(Blazor)::: file uploads</span></span>

<span data-ttu-id="4640c-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="4640c-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="4640c-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4640c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4640c-106">Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4640c-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="4640c-107">Zawsze przestrzegaj najlepszych rozwiązań w zakresie zabezpieczeń przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="4640c-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="4640c-108">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="4640c-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="4640c-109">`InputFile` cm6long</span><span class="sxs-lookup"><span data-stu-id="4640c-109">`InputFile` component</span></span>

<span data-ttu-id="4640c-110">`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="4640c-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="4640c-111">Domyślnie użytkownik wybiera pojedyncze pliki.</span><span class="sxs-lookup"><span data-stu-id="4640c-111">By default, the user selects single files.</span></span> <span data-ttu-id="4640c-112">Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="4640c-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="4640c-113">Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.</span><span class="sxs-lookup"><span data-stu-id="4640c-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="4640c-114">Aby odczytać dane z pliku wybranego przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="4640c-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="4640c-115">Wywołaj `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` plik i odczytaj go ze zwróconego strumienia.</span><span class="sxs-lookup"><span data-stu-id="4640c-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="4640c-116">Aby uzyskać więcej informacji, zobacz sekcję [strumienie plików](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="4640c-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="4640c-117">Wartość <xref:System.IO.Stream> zwracana przez `OpenReadStream` wymusza maksymalny rozmiar w bajtach `Stream` odczytywanych.</span><span class="sxs-lookup"><span data-stu-id="4640c-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="4640c-118">Domyślnie tylko pliki o rozmiarze mniejszym niż 524 288 KB (512 KB) mogą być odczytywane, zanim inne operacje odczytu spowodują wystąpienie wyjątku.</span><span class="sxs-lookup"><span data-stu-id="4640c-118">By default, only files smaller than 524,288 KB (512 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="4640c-119">Ten limit jest obecny, aby uniemożliwić deweloperom przypadkowe odczytywanie dużych plików w pamięci.</span><span class="sxs-lookup"><span data-stu-id="4640c-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="4640c-120">`maxAllowedSize`Parametr on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` może służyć do określania większego rozmiaru, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="4640c-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="4640c-121">Należy unikać odczytywania przychodzącego strumienia plików bezpośrednio do pamięci.</span><span class="sxs-lookup"><span data-stu-id="4640c-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="4640c-122">Na przykład nie Kopiuj bajtów pliku do <xref:System.IO.MemoryStream> lub Odczytaj jako tablicę bajtów.</span><span class="sxs-lookup"><span data-stu-id="4640c-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="4640c-123">Te podejścia mogą powodować problemy z wydajnością i zabezpieczeniami, szczególnie w :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4640c-123">These approaches can result in performance and security problems, especially in :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="4640c-124">Zamiast tego należy rozważyć kopiowanie bajtów plików do magazynu zewnętrznego, takiego jak obiekt BLOB lub plik na dysku.</span><span class="sxs-lookup"><span data-stu-id="4640c-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="4640c-125">Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4640c-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="4640c-126">Poniższy przykład ilustruje przekazywanie wielu plików obrazu w składniku.</span><span class="sxs-lookup"><span data-stu-id="4640c-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="4640c-127">`InputFileChangeEventArgs.GetMultipleFiles` umożliwia odczytywanie wielu plików.</span><span class="sxs-lookup"><span data-stu-id="4640c-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="4640c-128">Określ maksymalną liczbę plików, które należy odczytać, aby uniemożliwić złośliwemu użytkownikowi przekazanie większej liczby plików niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="4640c-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="4640c-129">`InputFileChangeEventArgs.File` zezwala na odczytywanie pierwszego i tylko pliku, jeśli przekazywanie plików nie obsługuje wielu plików.</span><span class="sxs-lookup"><span data-stu-id="4640c-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="4640c-130">`IBrowserFile` zwraca metadane [uwidocznione przez przeglądarkę](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako właściwości.</span><span class="sxs-lookup"><span data-stu-id="4640c-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="4640c-131">Te metadane mogą być przydatne do wstępnej weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="4640c-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="4640c-132">Na przykład zobacz [ `FileUpload.razor` i `FilePreview.razor` przykładowe składniki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="4640c-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="4640c-133">Strumienie plików</span><span class="sxs-lookup"><span data-stu-id="4640c-133">File streams</span></span>

<span data-ttu-id="4640c-134">W :::no-loc(Blazor WebAssembly)::: aplikacji dane są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4640c-134">In a :::no-loc(Blazor WebAssembly)::: app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="4640c-135">W :::no-loc(Blazor Server)::: aplikacji dane plików są przesyłane strumieniowo przez :::no-loc(SignalR)::: połączenie do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="4640c-135">In a :::no-loc(Blazor Server)::: app, the file data is streamed over the :::no-loc(SignalR)::: connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="4640c-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) umożliwia skonfigurowanie parametrów przekazywania plików dla programu :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4640c-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for :::no-loc(Blazor Server):::.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4640c-137">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="4640c-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
