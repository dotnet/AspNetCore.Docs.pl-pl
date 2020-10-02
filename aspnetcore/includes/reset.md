<span data-ttu-id="6b090-101">Wartość Resetuj umożliwia serwerowi zresetowanie żądania HTTP/2 z określonym kodem błędu.</span><span class="sxs-lookup"><span data-stu-id="6b090-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="6b090-102">Żądanie resetowania jest uznawane za przerwane.</span><span class="sxs-lookup"><span data-stu-id="6b090-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="6b090-103">`Reset` w poprzednim przykładzie kodu określa `INTERNAL_ERROR` Kod błędu.</span><span class="sxs-lookup"><span data-stu-id="6b090-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="6b090-104">Więcej informacji o kodach błędów protokołu HTTP/2 znajduje się w [sekcji kod błędu specyfikacji protokołu HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="6b090-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>