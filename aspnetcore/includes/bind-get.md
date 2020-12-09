> [!WARNING]
> <span data-ttu-id="5ea21-101">Ze względów bezpieczeństwa należy zadecydować, aby powiązać `GET` dane żądania z właściwościami modelu strony.</span><span class="sxs-lookup"><span data-stu-id="5ea21-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="5ea21-102">Sprawdź dane wejściowe użytkownika przed mapowaniem go na właściwości.</span><span class="sxs-lookup"><span data-stu-id="5ea21-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="5ea21-103">W `GET` przypadku scenariuszy, które opierają się na ciągach zapytania lub wartościach trasy, Metoda ta jest przydatna.</span><span class="sxs-lookup"><span data-stu-id="5ea21-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="5ea21-104">Aby powiązać właściwość na `GET` żądania, należy ustawić [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) `SupportsGet` właściwość atrybutu na `true` :</span><span class="sxs-lookup"><span data-stu-id="5ea21-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="5ea21-105">Aby uzyskać więcej informacji, zobacz [ASP.NET Core Community standup: bind on Get Discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="5ea21-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
