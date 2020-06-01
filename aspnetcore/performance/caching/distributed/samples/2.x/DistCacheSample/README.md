# <a name="aspnet-core-distributed-cache-sample"></a>Przykład ASP.NET Core rozproszonej pamięci podręcznej

Ten przykład ilustruje użycie rozproszonej pamięci podręcznej. Ten przykład pokazuje scenariusz opisany w temacie [współpraca z rozproszoną pamięcią podręczną w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

W środowisku produkcyjnym Przykładowa aplikacja jest skonfigurowana do używania rozproszonej pamięci podręcznej SQL Server. Aby ponownie skonfigurować aplikację do używania rozproszonej pamięci podręcznej Redis, Zmień dyrektywę preprocesora w górnej części pliku *Startup.cs* , aby użyć Redis ( `#define Redis // SQLServer` ). Aby uzyskać więcej informacji, zobacz [dyrektywy preprocesora w przykładowym kodzie](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
