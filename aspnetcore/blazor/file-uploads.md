---
title: ASP.NET Core Blazor operacje przekazywania plików
author: guardrex
description: Dowiedz się, jak przekazywać pliki w Blazor składniku plik_wejściowy.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722996"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="a033b-103">ASP.NET Core Blazor operacje przekazywania plików</span><span class="sxs-lookup"><span data-stu-id="a033b-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="a033b-104">Autor [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a033b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a033b-105">Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.</span><span class="sxs-lookup"><span data-stu-id="a033b-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="a033b-106">`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="a033b-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="a033b-107">Domyślnie użytkownik wybiera pojedyncze pliki.</span><span class="sxs-lookup"><span data-stu-id="a033b-107">By default, the user selects single files.</span></span> <span data-ttu-id="a033b-108">Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="a033b-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="a033b-109">Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.</span><span class="sxs-lookup"><span data-stu-id="a033b-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="a033b-110">Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a033b-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="a033b-111">Poniższy przykład ilustruje przekazywanie wielu plików obrazu w składniku:</span><span class="sxs-lookup"><span data-stu-id="a033b-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="a033b-112">Aby odczytać dane z pliku wybranego przez użytkownika, wywołaj `OpenReadStream` plik i odczytaj go ze zwróconego strumienia.</span><span class="sxs-lookup"><span data-stu-id="a033b-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="a033b-113">W Blazor WebAssembly aplikacji dane są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a033b-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="a033b-114">W Blazor Server aplikacji dane plików są przesyłane strumieniowo do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="a033b-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
