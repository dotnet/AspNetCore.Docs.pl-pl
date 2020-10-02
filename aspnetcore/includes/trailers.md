<span data-ttu-id="51e2c-101">Przyczepy HTTP są podobne do nagłówków HTTP, z tą różnicą, że są wysyłane po wysłaniu treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="51e2c-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="51e2c-102">W przypadku usług IIS i HTTP.SYS obsługiwane są tylko przyczepy z odpowiedzią HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="51e2c-102">For IIS and HTTP.SYS, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="51e2c-103">W poprzednim przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="51e2c-103">In the preceding example code:</span></span>

* <span data-ttu-id="51e2c-104">`SupportsTrailers` zapewnia, że przyczepy są obsługiwane na potrzeby odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="51e2c-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="51e2c-105">`DeclareTrailer` Dodaje nazwę danej przyczepy do `Trailer` nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="51e2c-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="51e2c-106">Deklarowanie przyczep z odpowiedzią jest opcjonalne, ale zalecane.</span><span class="sxs-lookup"><span data-stu-id="51e2c-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="51e2c-107">Jeśli `DeclareTrailer` jest wywoływana, musi być przed wysłaniem nagłówków odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="51e2c-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="51e2c-108">`AppendTrailer` dołącza przyczepę.</span><span class="sxs-lookup"><span data-stu-id="51e2c-108">`AppendTrailer` appends the trailer.</span></span>
