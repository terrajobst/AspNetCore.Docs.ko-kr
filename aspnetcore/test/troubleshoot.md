---
title: ASP.NET Core 프로젝트 문제 해결 및 디버깅
author: Rick-Anderson
description: ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354705"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core 프로젝트 문제 해결 및 디버깅

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

다음 링크는 문제 해결 지침을 제공 합니다.

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC 컨퍼런스 (런던, 2018): ASP.NET Core 응용 프로그램의 문제 진단](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET 블로그: ASP.NET Core 성능 문제 해결](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK 경고

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK 32 비트 및 64 비트 버전이 모두 설치 되어 있습니다.

ASP.NET Core에 대 한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.

> .NET Core SDK 32 비트 및 64 비트 버전이 모두 설치 되어 있습니다. ' C:\\Program Files\\dotnet\\sdk\\'에 설치 된 64 비트 버전의 템플릿만 표시 됩니다.

이 경고는 32 비트 (x86) 및 64 비트 (x64) 버전의 [.NET Core SDK](https://www.microsoft.com/net/download/all) 모두 설치 된 경우에 나타납니다. 두 버전 모두 설치 될 수 있는 일반적인 이유는 다음과 같습니다.

* 원래 32 비트 컴퓨터를 사용 하 여 .NET Core SDK 설치 관리자를 다운로드 한 다음,이를 전체에 복사 하 고 64 비트 컴퓨터에 설치 했습니다.
* 32 비트 .NET Core SDK 다른 응용 프로그램에 의해 설치 되었습니다.
* 잘못 된 버전이 다운로드 및 설치 되었습니다.

이 경고를 방지 하려면 32 비트 .NET Core SDK를 제거 합니다. **프로그램을 제거 하거나 변경** > **제어판** 에서 **프로그램 및 기능** > 제거 합니다. 경고가 발생 하는 이유와 그에 대 한 영향을 이해 하는 경우 경고를 무시할 수 있습니다.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK 여러 위치에 설치 되어 있습니다.

ASP.NET Core에 대 한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.

> .NET Core SDK는 여러 위치에 설치 됩니다. ' C:\\Program Files\\dotnet\\sdk\\'에 설치 된 Sdk의 템플릿만 표시 됩니다.

하나 이상의.NET Core SDK 설치 디렉터리 외부에 있는 경우에이 메시지를 참조 하세요 *c:\\Program Files\\dotnet\\sdk\\* 합니다. 일반적으로이는 .NET Core SDK MSI 설치 관리자 대신 복사/붙여넣기를 사용 하 여 컴퓨터에 배포 된 경우에 발생 합니다.

이 경고를 방지 하려면 모든 32 비트 .NET Core Sdk 및 런타임을 제거 합니다. **프로그램을 제거 하거나 변경** > **제어판** 에서 **프로그램 및 기능** > 제거 합니다. 경고가 발생 하는 이유와 그에 대 한 영향을 이해 하는 경우 경고를 무시할 수 있습니다.

### <a name="no-net-core-sdks-were-detected"></a>.NET Core Sdk가 검색 되지 않았습니다.

* ASP.NET Core에 대 한 Visual Studio **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.

  > .NET Core Sdk가 검색 되지 않았습니다. `PATH`환경 변수에 포함 되어 있는지 확인 하세요.

* `dotnet` 명령을 실행 하면 경고가 다음과 같이 표시 됩니다.

  > 설치 된 dotnet Sdk를 찾을 수 없습니다.

이러한 경고는 환경 변수가 컴퓨터의 .NET Core Sdk를 가리키지 `PATH` 경우에 나타납니다. 이 문제를 해결하려면 다음을 수행합니다.

* .NET Core SDK를 설치합니다. [.Net 다운로드](https://dotnet.microsoft.com/download)에서 최신 설치 관리자를 가져옵니다.
* `PATH` 환경 변수가 SDK가 설치 된 위치를 가리키는지 확인 합니다 (`C:\Program Files\dotnet\` 64 비트/x 64의 경우 또는 32 비트/x 86 `C:\Program Files (x86)\dotnet\`의 경우). SDK 설치 관리자는 일반적으로 `PATH`를 설정 합니다. 항상 동일한 비트 Sdk 및 런타임을 동일한 컴퓨터에 설치 합니다.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들을 설치한 후 SDK가 누락 됨

.Net core [호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) 을 설치 하면 .net core (`C:\Program Files (x86)\dotnet\`)의 32 비트 (x86) 버전을 가리키도록 .net core 런타임을 설치할 때 `PATH`가 수정 됩니다. 32 비트 (x86) .NET Core `dotnet` 명령을 사용 하는 경우 Sdk가 누락 될 수 있습니다 ([.Net Core sdk가 검색 되지 않음](#no-net-core-sdks-were-detected)). 이 문제를 해결 하려면 `C:\Program Files\dotnet\`를 `PATH``C:\Program Files (x86)\dotnet\` 하기 전의 위치로 이동 합니다.

## <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 미들웨어를 사용 하 여 앱에서 다음 데이터를 가져올 수 있습니다.

* 요청 &ndash; 메서드, 체계, 호스트, pathbase, 경로, 쿼리 문자열, 헤더
* 연결 &ndash; 원격 IP 주소, 원격 포트, 로컬 IP 주소, 로컬 포트, 클라이언트 인증서
* Id &ndash; 이름, 표시 이름
* 구성 설정
* 환경 변수

`Startup.Configure` 메서드의 요청 처리 파이프라인의 시작 부분에 다음 [미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 코드를 추가 합니다. 개발 환경 에서만 코드가 실행 되도록 하기 위해 미들웨어가 실행 되기 전에 환경이 검사 됩니다.

환경을 얻으려면 다음 방법 중 하나를 사용 합니다.

* `IHostingEnvironment`를 `Startup.Configure` 메서드에 삽입 하 고 지역 변수를 사용 하 여 환경을 확인 합니다. 다음 샘플 코드에서는이 방법을 보여 줍니다.

* `Startup` 클래스의 속성에 환경을 할당 합니다. 속성 (예: `if (Environment.IsDevelopment())`)을 사용 하 여 환경을 확인 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core 앱 디버그

다음 링크는 ASP.NET Core 앱을 디버깅 하는 방법에 대 한 정보를 제공 합니다.

* [Linux에서 ASP Core 디버깅](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH를 통해 Unix의 .NET Core 디버깅](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [빠른 시작: Visual Studio 디버거로 디버그 ASP.NET](/visualstudio/debugger/quickstart-debug-aspnet)
* 자세한 디버깅 정보는 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/2960) 를 참조 하세요.
