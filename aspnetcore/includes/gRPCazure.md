## <a name="grpc-not-supported-on-azure-app-service"></a>GRPC nie jest obsługiwany w usłudze Azure App Service

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) nie jest obecnie obsługiwany w usłudze Azure App Service lub usługach IIS. Http/2 implementacji Http.Sys nie obsługuje odpowiedzi HTTP końcowe nagłówki, które gRPC opiera się na. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore/issues/9020).
