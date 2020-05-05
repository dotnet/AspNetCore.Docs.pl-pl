> [!WARNING]
> <span data-ttu-id="234e2-101">Parametr **"catch-all"** może być zgodny z niepoprawnymi trasami z powodu [błędu](https://github.com/dotnet/aspnetcore/issues/18677) w routingu.</span><span class="sxs-lookup"><span data-stu-id="234e2-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="234e2-102">Aplikacje, na które ma wpływ ta usterka, mają następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="234e2-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="234e2-103">Trasa przechwycenia, na przykład`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="234e2-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="234e2-104">Trasa catch-all nie będzie zgodna z żądaniami, które powinny być zgodne.</span><span class="sxs-lookup"><span data-stu-id="234e2-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="234e2-105">Usunięcie innych tras spowoduje rozpoczęcie pracy z całą trasą.</span><span class="sxs-lookup"><span data-stu-id="234e2-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="234e2-106">Zobacz błędy usługi GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) i [16579](https://github.com/dotnet/aspnetcore/issues/16579) , aby zobaczyć przykładowe przypadki, w których trafili ten błąd.</span><span class="sxs-lookup"><span data-stu-id="234e2-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="234e2-107">Zaplanowano poprawkę wstępną dla tej usterki.</span><span class="sxs-lookup"><span data-stu-id="234e2-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="234e2-108">Ten dokument zostanie zaktualizowany po wydaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="234e2-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="234e2-109">Po wydaniu poprawki Poniższy kod ustawi przełącznik wewnętrzny, który naprawia tę usterkę:</span><span class="sxs-lookup"><span data-stu-id="234e2-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```