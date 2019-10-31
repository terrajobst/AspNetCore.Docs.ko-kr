## <a name="grpc-not-supported-on-azure-app-service"></a>Azure App Service에서 gRPC가 지원되지 않음

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index)는 현재 Azure App Service 또는 IIS에서 지원되지 않습니다. Http.sys의 HTTP/2 구현은 gRPC가 사용하는 HTTP 응답 후행 헤더를 지원하지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/aspnet/AspNetCore/issues/9020)를 참조하세요.
