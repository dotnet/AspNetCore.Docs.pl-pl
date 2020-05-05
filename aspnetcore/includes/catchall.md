> [!WARNING]
> Parametr **"catch-all"** może być zgodny z niepoprawnymi trasami z powodu [błędu](https://github.com/dotnet/aspnetcore/issues/18677) w routingu. Aplikacje, na które ma wpływ ta usterka, mają następującą charakterystykę:
>
> * Trasa przechwycenia, na przykład`{**slug}"`
> * Trasa catch-all nie będzie zgodna z żądaniami, które powinny być zgodne.
> * Usunięcie innych tras spowoduje rozpoczęcie pracy z całą trasą.
>
> Zobacz błędy usługi GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) i [16579](https://github.com/dotnet/aspnetcore/issues/16579) , aby zobaczyć przykładowe przypadki, w których trafili ten błąd.
>
> Zaplanowano poprawkę wstępną dla tej usterki. Ten dokument zostanie zaktualizowany po wydaniu poprawki. Po wydaniu poprawki Poniższy kod ustawi przełącznik wewnętrzny, który naprawia tę usterkę:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```