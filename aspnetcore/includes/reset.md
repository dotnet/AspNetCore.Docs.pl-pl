Wartość Resetuj umożliwia serwerowi zresetowanie żądania HTTP/2 z określonym kodem błędu. Żądanie resetowania jest uznawane za przerwane.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` w poprzednim przykładzie kodu określa `INTERNAL_ERROR` Kod błędu. Więcej informacji o kodach błędów protokołu HTTP/2 znajduje się w [sekcji kod błędu specyfikacji protokołu HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).