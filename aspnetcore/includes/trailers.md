Przyczepy HTTP są podobne do nagłówków HTTP, z tą różnicą, że są wysyłane po wysłaniu treści odpowiedzi. W przypadku usług IIS i HTTP.sys obsługiwane są tylko przyczepy z odpowiedzią HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

W poprzednim przykładowym kodzie:

* `SupportsTrailers` zapewnia, że przyczepy są obsługiwane na potrzeby odpowiedzi.
* `DeclareTrailer` Dodaje nazwę danej przyczepy do `Trailer` nagłówka odpowiedzi. Deklarowanie przyczep z odpowiedzią jest opcjonalne, ale zalecane. Jeśli `DeclareTrailer` jest wywoływana, musi być przed wysłaniem nagłówków odpowiedzi.
* `AppendTrailer` dołącza przyczepę.
