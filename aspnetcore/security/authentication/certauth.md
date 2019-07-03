---
title: ASP.NET Core에서 인증서 인증을 구성 합니다.
author: blowdart
description: ASP.NET Core에서 HTTP.sys 및 IIS에 대 한 인증서 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538717"
---
# <a name="overview"></a>개요

`Microsoft.AspNetCore.Authentication.Certificate` 유사한 구현을 포함 [인증서 인증](https://tools.ietf.org/html/rfc5246#section-7.4.4) ASP.NET Core에 대 한 합니다. 인증서 인증 적이 ASP.NET Core 도달 하기 전에 긴 TLS 수준에서 발생 합니다. 보다 정확히 말해이 인증서의 유효성을 검사 하 고 그런 다음, 해당 인증서를 해결할 수 있는 이벤트를 제공 하는 인증 처리기를 `ClaimsPrincipal`입니다. 

[호스트 구성](#configure-your-host-to-require-certificates) 인증서 인증을 위한 것 IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 합니다.

## <a name="get-started"></a>시작

HTTPS 인증서를 획득, 적용 및 [호스트 구성](#configure-your-host-to-require-certificates) 인증서를 요구 하도록 합니다.

웹 앱에 대 한 참조를 추가 합니다 `Microsoft.AspNetCore.Authentication.Certificate` 패키지 있습니다. 그런 다음 합니다 `Startup.Configure` 메서드를 호출 `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` 옵션을 사용 하 여에 대 한 대리자를 제공 하 `OnCertificateValidated` 요청과 함께 전송 된 클라이언트 인증서의 보조 유효성 검사를 수행 합니다. 설정에 해당 정보를 `ClaimsPrincipal` 에 설정는 `context.Principal` 속성.

이 처리기가 반환 하는 경우 인증에 실패 하면를 `403 (Forbidden)` 응답 대신는 `401 (Unauthorized)`예상할 수 있듯이, 합니다. 그 이유는 인증 초기 TLS 연결 하는 동안 발생 해야 함을 합니다. 처리기에 도달한 시간, 때는 너무 늦은 것입니다. 인증서를 사용 하 여 하나에 연결이 익명 연결을 통해 업그레이드 방법이 없습니다.

이 밖에도 `app.UseAuthentication();` 에 `Startup.Configure` 메서드. 그렇지 않은 경우는 HttpContext.User 됩니다로 설정할 수 `ClaimsPrincipal` 인증서에서 생성 합니다. 예를 들어:

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

앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다. 처리기를 공용 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.

## <a name="configure-certificate-validation"></a>인증서 유효성 검사 구성

`CertificateAuthenticationOptions` 처리기에는 인증서에 수행 해야 하는 최소 유효성 검사는 일부 기본 제공 유효성 검사 합니다. 이러한 각 설정은 기본적으로 사용 됩니다.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = 자체 서명, 또는 모든 연결 (연결 | 자체 서명)

이 검사의 적절 한 인증서 형식에만 허용 됩니다 유효성을 검사 합니다.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 Eku 없음 또는 키 사용 (EKU) 전혀 확장을 확인 합니다. 사양 들어 eku가 없어야 지정 된 경우에 따라 다음 모든 Eku는 유효한 것으로 간주 합니다.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다. 각 요청에 처리기를 현재 세션에 해당 하는 동안 제공 된 경우 유효 했던 인증서 만료 되지 않았는지를 확인 합니다.

### <a name="revocationflag"></a>RevocationFlag

체인의 인증서 해지 확인을 지정 하는 플래그입니다.

해지 검사는 인증서는 루트 인증서에 연결 하는 경우에 수행 됩니다.

### <a name="revocationmode"></a>RevocationMode

해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.

인증 기관에 연결 하는 동안 오래 지연에서 온라인 확인을 지정 될 수 있습니다.

해지 검사는 인증서는 루트 인증서에 연결 하는 경우에 수행 됩니다.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>특정 경로에 인증서를 요구 하도록 내 앱을 구성할 수 있나요?

이것이 불가능 합니다. 인증서 교환을 HTTPS 대화의 시작을 완료 되는 서버에서 모든 요청 필드를 기반으로 하는 범위에 불가능 하므로 해당 연결에서 첫 번째 요청이 수신 되기 전에 수행 해야 합니다.

## <a name="handler-events"></a>이벤트 처리기

처리기에 두 개의 이벤트가 있습니다.

* `OnAuthenticationFailed` &ndash; 예외가 인증 하는 동안 발생 하 고 대응할 수 하는 경우 호출 됩니다.
* `OnCertificateValidated` &ndash; 인증서를 확인, 유효성 검사를 통과 하 고 기본 보안 주체를 만든 후 호출 됩니다. 이 이벤트를 사용 하면 고유한 유효성 검사를 수행 하 고 확장 하거나 보안 주체를 바꿀 수 있습니다. 예제를 보려면 다음을 포함 합니다.
  * 서비스에 인증서를 식별 하는 경우를 결정 합니다.
  * 사용자 고유의 보안 주체를 생성합니다. `Startup.ConfigureServices`에서 다음 예제를 참조하세요.

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

인바운드 인증서 추가 유효성 검사를 충족 하지 않는 경우 호출 `context.Fail("failure reason")` 실패 이유를 사용 하 여 합니다.

실제 기능을 원할 것 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 합니다. 대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다. `Startup.ConfigureServices`에서 다음 예제를 참조하세요.

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

개념적으로 인증서의 유효성 검사는 권한 부여 문제입니다. 예를 들어, 한 검사를 추가, 발급자 또는 내부 대신 권한 부여 정책에서 지문을 `OnCertificateValidated`를 완벽 하 게 합니다.

## <a name="configure-your-host-to-require-certificates"></a>인증서를 요구 하도록 호스트를 구성 합니다.

### <a name="kestrel"></a>Kestrel

*Program.cs*, 아래와 같이 Kestrel을 구성 합니다.

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

1. 사이트를 선택 합니다 **연결** 탭 합니다.
1. 두 번 클릭 합니다 **SSL 설정** 옵션을 **기능 보기** 창입니다.
1. 확인 합니다 **SSL 필요** 확인란을 선택 하 고는 **필요** 라디오 단추를 **클라이언트 인증서** 섹션.

![IIS에서 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 및 사용자 지정 웹 프록시

참조 된 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 미들웨어를 전달 하는 인증서를 구성 하는 방법에 대 한 합니다.
