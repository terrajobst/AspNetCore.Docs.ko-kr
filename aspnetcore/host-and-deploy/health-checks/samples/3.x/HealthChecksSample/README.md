# <a name="aspnet-core-health-check-sample"></a>ASP.NET Core 상태 검사 샘플

이 샘플에는 상태 검사 미들웨어 및 서비스의 사용을 보여줍니다. 이 샘플은 [ASP.NET Core의 상태 검사](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) 항목에 설명된 시나리오를 보여줍니다.

항목에서 설명된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) 명령을 사용합니다. 탐색 중인 시나리오에 대한 스위치를 전달합니다. `dotnet run`에 스위치가 제공되지 않은 경우 앱의 기본 설정은 `basic`입니다.

| 시나리오                                               | 샘플 앱 명령               | 설명 |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| 기본 상태 프로브(기본값)                           | `dotnet run --scenario basic`    | 앱이 HTTP 요청을 처리할 수 있는지 확인합니다. |
| 데이터베이스 프로브                                         | `dotnet run --scenario db`       | SQL Server 데이터베이스 연결을 확인합니다. 지침은 항목의 [데이터베이스 프로브](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) 섹션을 참조하세요. |
| 준비 상태/활동성 프로브                              | `dotnet run --scenario liveness` | 라이브 앱 상태(*활동성*)와 라이브를 준비하는 앱(*준비 상태*)에 대한 검사를 수행합니다. |
| 메트릭 기반 프로브(메모리)/<br>사용자 지정 응답 기록기 | `dotnet run --scenario writer`   | 상태 엔드포인트가 확인되면 메모리 사용에 대해 검사하고 사용자 정의 JSON을 기록합니다. |
| 포트별 필터링                                         | `dotnet run --scenario port`     | 지정된 포트에 대해 상태 검사를 필터링합니다. 지침은 항목의 [포트별 필터링](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) 섹션을 참조하세요. |

데이터베이스 프로브 및 포트 필터 시나리오에는 추가 구성이 필요합니다. 자세한 내용은 [상태 검사](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) 항목을 참조하세요.
