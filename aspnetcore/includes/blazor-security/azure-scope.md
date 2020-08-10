> [!NOTE]
> <span data-ttu-id="9ecd0-101">Jeśli Azure Portal udostępnia identyfikator URI zakresu dla aplikacji, a aplikacja zgłasza nieobsłużony wyjątek, gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="9ecd0-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="9ecd0-102">Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="9ecd0-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="9ecd0-103">Spróbuj dostarczyć identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="9ecd0-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> <span data-ttu-id="9ecd0-104">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="9ecd0-104">For example:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
