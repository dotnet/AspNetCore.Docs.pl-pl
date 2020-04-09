> [!WARNING]
> Ze względów bezpieczeństwa należy `GET` wyrazić zgodę na powiązanie danych żądania do właściwości modelu strony. Sprawdź dane wejściowe użytkownika przed mapowaniem go do właściwości. Decydując się `GET` na powiązanie jest przydatne podczas adresowania scenariuszy, które opierają się na ciąg kwerendy lub wartości trasy.
>
> Aby powiązać `GET` właściwość na [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) żądaniach, `SupportsGet` ustaw `true`właściwość atrybutu na:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Aby uzyskać więcej informacji, zobacz [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
