> [!WARNING]
> <span data-ttu-id="897ea-101">Ze względów bezpieczeństwa należy `GET` wyrazić zgodę na powiązanie danych żądania do właściwości modelu strony.</span><span class="sxs-lookup"><span data-stu-id="897ea-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="897ea-102">Sprawdź dane wejściowe użytkownika przed mapowaniem go do właściwości.</span><span class="sxs-lookup"><span data-stu-id="897ea-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="897ea-103">Decydując się `GET` na powiązanie jest przydatne podczas adresowania scenariuszy, które opierają się na ciąg kwerendy lub wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="897ea-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="897ea-104">Aby powiązać `GET` właściwość na [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) żądaniach, `SupportsGet` ustaw `true`właściwość atrybutu na:</span><span class="sxs-lookup"><span data-stu-id="897ea-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="897ea-105">Aby uzyskać więcej informacji, zobacz [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="897ea-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
