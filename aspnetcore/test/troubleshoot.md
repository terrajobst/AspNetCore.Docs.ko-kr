---
title: ASP.NET Core 프로젝트 문제 해결
author: Rick-Anderson
description: ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: test/troubleshoot
ms.openlocfilehash: bcec8a55a5111e1f3acf53ae2f57b45e6e609d25
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313682"
---
# <a name="troubleshoot-aspnet-core-projects"></a>ASP.NET Core 프로젝트 문제 해결

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

다음 링크를 문제 해결 지침을 제공합니다.

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC 회의 (런던, 2018): ASP.NET Core 응용 프로그램에서 문제 진단](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET 블로그: ASP.NET Core 성능 문제 해결](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET core SDK 경고

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK의 32 비트 및 64 비트 버전이 모두 설치 되어

에 **새 프로젝트** 대화 ASP.NET Core에 대 한 다음과 같은 경고가 표시 될 수 있습니다.

> .NET Core SDK의 32 비트 및 64 비트 버전이 설치 됩니다. 에 설치 된 64 비트 버전의 템플릿만 ' c:\\Program Files\\dotnet\\sdk\\' 표시 됩니다.

이 경고를 표시 하는 경우 32 비트 (x86)와 버전의 64 비트 (x64) 합니다 [.NET Core SDK](https://www.microsoft.com/net/download/all) 설치 됩니다. 두 버전을 설치할 수 있습니다 하는 일반적인 이유는 다음과 같습니다.

* 원래 32 비트 컴퓨터를 사용 하 여.NET Core SDK 설치 관리자를 다운로드 하지만 다음 복사 하는 것을 64 비트 컴퓨터에 설치.
* 32 비트.NET Core SDK가 다른 응용 프로그램으로 설치 됩니다.
* 잘못 된 버전 다운로드 되어 설치 됩니다.

이 경고를 방지 하기 위해 32 비트.NET Core SDK를 제거 합니다. 제거할 **Control Panel** > **프로그램 및 기능** > **제거 또는 변경 프로그램**합니다. 경고가 발생 하는 이유 및 그 의미를 이해 하면 경고를 무시할 수 있습니다.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>여러 위치에서.NET Core SDK가 설치

에 **새 프로젝트** 대화 ASP.NET Core에 대 한 다음과 같은 경고가 표시 될 수 있습니다.

> .NET Core SDK는 여러 위치에 설치 됩니다. 에 설치 된 Sdk의 템플릿만 ' c:\\Program Files\\dotnet\\sdk\\' 표시 됩니다.

하나 이상의.NET Core SDK 설치 디렉터리 외부에 있는 경우에이 메시지를 참조 하세요 *c:\\Program Files\\dotnet\\sdk\\* 합니다. 일반적으로.NET Core SDK MSI 설치 관리자 대신 복사/붙여넣기를 사용 하는 컴퓨터에 배포 된 경우 발생 합니다.

모든 32 비트.NET Core Sdk 및 런타임을이 경고를 방지 하기 위해 제거 합니다. 제거할 **Control Panel** > **프로그램 및 기능** > **제거 또는 변경 프로그램**합니다. 경고가 발생 하는 이유 및 그 의미를 이해 하면 경고를 무시할 수 있습니다.

### <a name="no-net-core-sdks-were-detected"></a>.NET Core Sdk 발견

* Visual studio에서 **새 프로젝트** 대화 ASP.NET Core에 대 한 다음과 같은 경고가 표시 될 수 있습니다.

  > .NET Core Sdk 발견, 환경 변수에 포함 되도록 `PATH`합니다.

* 실행 하는 경우는 `dotnet` 명령으로 경고가 표시 됩니다.

  > 설치 된 모든 dotnet Sdk를 찾을 수 없습니다.

이러한 경고를 표시 하는 경우 환경 변수 `PATH` 컴퓨터에서 모든.NET Core Sdk를 가리키지 않습니다. 이 문제를 해결 하려면

* .NET Core SDK를 설치합니다. 최신 설치 관리자를 가져옵니다 [.NET 다운로드](https://dotnet.microsoft.com/download)합니다.
* 있는지 확인 합니다 `PATH` 환경 변수는 SDK를 설치한 위치를 가리키는 (`C:\Program Files\dotnet\` 64 비트 x64에 대 한 또는 `C:\Program Files (x86)\dotnet\` 32-bit/x86 용). SDK 설치 관리자가 일반적으로 설정 된 `PATH`합니다. 항상 동일한 비트 Sdk 및 런타임을 동일한 컴퓨터에 설치 합니다.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들을 설치한 후 누락 된 SDK

설치를 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) 수정 합니다 `PATH` .NET Core의 32 비트 (x86) 버전을 가리키도록.NET Core 런타임을 설치 하면 (`C:\Program Files (x86)\dotnet\`). Sdk 누락이 발생할 수 있습니다 때 32 비트 (x86).NET Core `dotnet` 명령을 사용 하 여 ([.NET Core Sdk 발견](#no-net-core-sdks-were-detected)). 이 문제를 해결 하려면 이동할 `C:\Program Files\dotnet\` 를 앞으로 `C:\Program Files (x86)\dotnet\` 에 `PATH`합니다.

## <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱 요청에 응답할 수 인 경우에 미들웨어를 사용 하 여 앱에서 다음 데이터를 가져올 수 있습니다.

* 요청 &ndash; 메서드를 체계, 호스트, pathbase, 경로, 쿼리 문자열, 헤더
* 연결 &ndash; 원격 IP 주소, 포트를 원격, 로컬 IP 주소, 로컬 포트, 클라이언트 인증서
* Identity &ndash; 이름, 표시 이름
* 구성 설정
* 환경 변수

다음 배치 [미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 맨 앞에 코드를 `Startup.Configure` 메서드 요청 처리 파이프라인. 미들웨어는 코드를 개발 환경에만 실행 되도록 실행 되기 전에 환경을 확인 합니다.

환경을 얻으려면 다음 방법 중 하나를 사용 합니다.

* 삽입 합니다 `IHostingEnvironment` 에 `Startup.Configure` 메서드 및 지역 변수를 사용 하 여 환경 확인 합니다. 다음 샘플 코드에는이 방법을 보여 줍니다.

* 환경 속성에 할당 된 `Startup` 클래스입니다. 확인 속성을 사용 하 여 환경 (예를 들어 `if (Environment.IsDevelopment())`).

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
