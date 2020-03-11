# <a name="aspnet-core-distributed-cache-sample"></a>ASP.NET Core 분산 캐시 샘플

이 샘플에서는 분산 캐시를 사용 하는 방법을 보여 줍니다. 이 샘플에서는 [ASP.NET Core의 분산 캐시 작업](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) 항목에서 설명 하는 시나리오를 보여 줍니다.

프로덕션 환경에서 샘플 앱은 분산 SQL Server 캐시를 사용 하도록 구성 됩니다. Distributed Redis cache를 사용 하도록 앱을 다시 구성 하려면 *Startup.cs* 파일 맨 위의 전처리기 지시문을 Redis (`#define Redis // SQLServer`)를 사용 하도록 변경 합니다. 자세한 내용은 [샘플 코드의 전처리기 지시문](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code)을 참조 하세요.
