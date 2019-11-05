---
title: ASP.NET Core에서 인증서 인증 구성
author: blowdart
description: IIS 및 HTTP.SYS 용 ASP.NET Core에서 인증서 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: 1e646aabb4e384e6906575e7beaa680e91f968a0
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616572"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>ASP.NET Core에서 인증서 인증 구성

`Microsoft.AspNetCore.Authentication.Certificate`에는 ASP.NET Core에 대 한 [인증서 인증과](https://tools.ietf.org/html/rfc5246#section-7.4.4) 비슷한 구현이 포함 되어 있습니다. 인증서 인증은 ASP.NET Core 하기 전에는 매우 긴 TLS 수준에서 발생 합니다. 보다 정확 하 게,이는 인증서의 유효성을 검사 한 다음 해당 인증서를 `ClaimsPrincipal`에 대해 확인할 수 있는 이벤트를 제공 하는 인증 처리기입니다. 

인증서 인증을 위해 [호스트를 구성](#configure-your-host-to-require-certificates) 하 고, IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 모든 항목을 사용 합니다.

## <a name="proxy-and-load-balancer-scenarios"></a>프록시 및 부하 분산 장치 시나리오

인증서 인증은 프록시 또는 부하 분산 장치가 클라이언트와 서버 간의 트래픽을 처리 하지 않는 경우 주로 사용 되는 상태 저장 시나리오입니다. 프록시 또는 부하 분산 장치를 사용 하는 경우 인증서 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.

* 인증을 처리 합니다.
* 인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.

프록시 및 부하 분산 장치를 사용 하는 환경에서 인증서 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.

## <a name="get-started"></a>시작

HTTPS 인증서를 획득 하 고 적용 하며 인증서를 요구 하도록 [호스트를 구성](#configure-your-host-to-require-certificates) 합니다.

웹 앱에서 `Microsoft.AspNetCore.Authentication.Certificate` 패키지에 대 한 참조를 추가 합니다. 그런 다음 `Startup.ConfigureServices` 메서드에서 옵션과 함께 `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`를 호출 하 여 요청과 함께 전송 된 클라이언트 인증서에 대 한 모든 보충 유효성 검사를 수행할 수 있는 `OnCertificateValidated` 대리자를 제공 합니다. 해당 정보를 `ClaimsPrincipal` 설정 하 고 `context.Principal` 속성에 설정 합니다.

인증이 실패 하는 경우이 처리기는 `401 (Unauthorized)``403 (Forbidden)` 응답을 반환 합니다. 초기 TLS 연결 중에 인증이 수행 되어야 한다는 것을 의미 합니다. 처리기에 도달할 때까지 너무 늦습니다. 익명 연결에서 인증서를 사용 하는 연결로의 연결을 업그레이드할 수 있는 방법은 없습니다.

또한 `Startup.Configure` 메서드에 `app.UseAuthentication();`를 추가 합니다. 그렇지 않으면 HttpContext는 인증서에서 만든 `ClaimsPrincipal` 설정 되지 않습니다. 예를 들면,

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다. 처리기는 일반적인 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.

## <a name="configure-certificate-validation"></a>인증서 유효성 검사 구성

`CertificateAuthenticationOptions` 처리기에는 인증서에 대해 수행 해야 하는 최소한의 유효성 검사를 수행 하는 몇 가지 기본 제공 유효성 검사가 있습니다. 이러한 각 설정은 기본적으로 사용 하도록 설정 되어 있습니다.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = 체인, SelfSigned 또는 모두 (연결 된 | SelfSigned)

이 검사는 적절 한 인증서 유형만 허용 되는지 확인 합니다.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 EKU (확장 키 사용)가 있거나 Eku가 없는지 확인 합니다. 지정 된 것 처럼 EKU가 지정 되지 않은 경우 모든 Eku가 유효한 것으로 간주 됩니다.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다. 각 요청에서 처리기는 제공 된 유효한 인증서가 현재 세션 중에 만료 되지 않도록 합니다.

### <a name="revocationflag"></a>RevocationFlag

체인에서 해지를 확인할 인증서를 지정 하는 플래그입니다.

해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.

### <a name="revocationmode"></a>RevocationMode

해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.

온라인 검사를 지정 하면 인증 기관에 연결 하는 동안 지연 시간이 길어질 수 있습니다.

해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>특정 경로에 대해서만 인증서를 요구 하도록 앱을 구성할 수 있나요?

이는 불가능 합니다. 인증서 교환은 HTTPS 대화가 시작 되 면 서버에서 첫 번째 요청을 수신 하기 전에 수행 하는 것 이므로 요청 필드를 기준으로 범위를 지정할 수 없습니다.

## <a name="handler-events"></a>처리기 이벤트

처리기에는 두 가지 이벤트가 있습니다.

* 인증 하는 동안 예외가 발생 하 고 대응할 수 있는 경우 `OnAuthenticationFailed` &ndash; 호출 됩니다.
* 인증서의 유효성을 검사 하 고 유효성 검사를 통과 하 여 기본 보안 주체를 만든 후에 호출 &ndash; `OnCertificateValidated`. 이 이벤트를 사용 하 여 사용자 고유의 유효성 검사를 수행 하 고 보안 주체를 확대 하거나 바꿀 수 있습니다. 예를 들면 다음과 같습니다.
  * 인증서가 서비스에 알려져 있는지 확인 하는 중입니다.
  * 사용자 고유의 보안 주체를 구성 합니다. `Startup.ConfigureServices`에서 다음 예제를 참조하세요.

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

인바운드 인증서가 추가 유효성 검사를 충족 하지 않는 경우 실패 이유를 사용 하 여 `context.Fail("failure reason")`를 호출 합니다.

실제 기능을 사용 하려면 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 하는 것이 좋습니다. 대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다. `Startup.ConfigureServices`에서 다음 예제를 참조하세요.

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

개념적으로 인증서의 유효성 검사는 권한 부여에 대 한 문제입니다. 예를 들어 `OnCertificateValidated`에 포함 되지 않고 권한 부여 정책의 발급자 또는 지문을 추가 하는 것은 완벽 하 게 사용할 수 있습니다.

## <a name="configure-your-host-to-require-certificates"></a>인증서를 요구 하도록 호스트 구성

### <a name="kestrel"></a>Kestrel

*Program.cs*에서 다음과 같이 Kestrel을 구성 합니다.

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a>IIS

IIS 관리자에서 다음 단계를 완료 합니다.

1. **연결** 탭에서 사이트를 선택 합니다.
1. **기능 보기** 창에서 **SSL 설정** 옵션을 두 번 클릭 합니다.
1. **SSL 필요** 확인란을 선택 하 고 **클라이언트 인증서** 섹션 **에서 라디오 단추를 선택** 합니다.

![IIS의 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 및 사용자 지정 웹 프록시

인증서 전달 미들웨어를 구성 하는 방법은 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 를 참조 하세요.
