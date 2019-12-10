---
title: ASP.NET Core에서 인증서 인증 구성
author: blowdart
description: IIS 및 HTTP.SYS 용 ASP.NET Core에서 인증서 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 12/09/2019
uid: security/authentication/certauth
ms.openlocfilehash: 38ee8a6767191bb3eee4286e49b96162b14d9889
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959062"
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

웹 앱에서 `Microsoft.AspNetCore.Authentication.Certificate` 패키지에 대 한 참조를 추가 합니다. 그런 다음 `Startup.ConfigureServices` 메서드에서 옵션과 함께 `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);`를 호출 하 여 요청과 함께 전송 된 클라이언트 인증서에 대 한 모든 보충 유효성 검사를 수행할 수 있는 `OnCertificateValidated` 대리자를 제공 합니다. 해당 정보를 `ClaimsPrincipal` 설정 하 고 `context.Principal` 속성에 설정 합니다.

인증이 실패 하는 경우이 처리기는 `401 (Unauthorized)``403 (Forbidden)` 응답을 반환 합니다. 초기 TLS 연결 중에 인증이 수행 되어야 한다는 것을 의미 합니다. 처리기에 도달할 때까지 너무 늦습니다. 익명 연결에서 인증서를 사용 하는 연결로의 연결을 업그레이드할 수 있는 방법은 없습니다.

또한 `Startup.Configure` 메서드에 `app.UseAuthentication();`를 추가 합니다. 그렇지 않으면 `HttpContext.User` 인증서에서 만든 `ClaimsPrincipal`로 설정 되지 않습니다. 예를 들면 다음과 같습니다.:

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

인증 기관에 연결 하는 동안 오래 지연에서 온라인 확인을 지정 될 수 있습니다.

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
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="iis"></a>IIS

IIS 관리자에서 다음 단계를 완료 합니다.

1. **연결** 탭에서 사이트를 선택 합니다.
1. **기능 보기** 창에서 **SSL 설정** 옵션을 두 번 클릭 합니다.
1. **SSL 필요** 확인란을 선택 하 고 **클라이언트 인증서** 섹션 **에서 라디오 단추를 선택** 합니다.

![IIS의 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 및 사용자 지정 웹 프록시

인증서 전달 미들웨어를 구성 하는 방법은 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 를 참조 하세요.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Azure Web Apps에서 인증서 인증 사용

`AddCertificateForwarding` 메서드는 다음을 지정 하는 데 사용 됩니다.

* 클라이언트 헤더 이름입니다.
* `HeaderConverter` 속성을 사용 하 여 인증서를 로드 하는 방법입니다.

Azure Web Apps에서 인증서는 `X-ARR-ClientCert`라는 사용자 지정 요청 헤더로 전달 됩니다. 이를 사용 하려면 `Startup.ConfigureServices`에서 인증서 전달을 구성 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-ARR-ClientCert";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

그러면 `Startup.Configure` 메서드가 미들웨어를 추가 합니다. `UseCertificateForwarding`는 `UseAuthentication`를 호출 하기 전에 호출 되며 `UseAuthorization`됩니다.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

별도의 클래스를 사용 하 여 유효성 검사 논리를 구현할 수 있습니다. 이 예제에서는 동일한 자체 서명 된 인증서를 사용 하기 때문에 인증서만 사용할 수 있는지 확인 합니다. 클라이언트 인증서와 서버 인증서의 지문이 일치 하는지 확인 합니다. 그렇지 않으면 인증서를 사용할 수 있으며 인증 하는 데 충분 합니다. 이는 `AddCertificate` 메서드 내에서 사용 됩니다. 중간 또는 자식 인증서를 사용 하는 경우 여기에서 주체 또는 발급자의 유효성을 검사할 수도 있습니다.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate"></a>인증서를 사용 하 여 HttpClient 구현

웹 API 클라이언트는 `IHttpClientFactory` 인스턴스를 사용 하 여 만든 `HttpClient`를 사용 합니다. 이는 `HttpClient`에 대 한 처리기를 정의 하는 방법을 제공 하지 않으므로 `HttpRequestMessage`를 사용 하 여 `X-ARR-ClientCert` 요청 헤더에 인증서를 추가 합니다. 인증서는 `GetRawCertDataString` 메서드를 사용 하 여 문자열로 추가 됩니다. 

```csharp
private async Task<JsonDocument> GetApiDataAsync()
{
    try
    {
        // Do not hardcode passwords in production code
        // Use thumbprint or key vault
        var cert = new X509Certificate2(
            Path.Combine(_environment.ContentRootPath, 
                "sts_dev_cert.pfx"), "1234");
        var client = _clientFactory.CreateClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = new Uri("https://localhost:44379/api/values"),
            Method = HttpMethod.Get,
        };

        request.Headers.Add("X-ARR-ClientCert", cert.GetRawCertDataString());
        var response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            var responseContent = await response.Content.ReadAsStringAsync();
            var data = JsonDocument.Parse(responseContent);

            return data;
        }

        throw new ApplicationException(
            $"Status code: {response.StatusCode}, " +
            $"Error: {response.ReasonPhrase}");
    }
    catch (Exception e)
    {
        throw new ApplicationException($"Exception {e}");
    }
}
```

서버에 올바른 인증서를 보내면 데이터가 반환 됩니다. 인증서 나 잘못 된 인증서를 보낸 경우 HTTP 403 상태 코드가 반환 됩니다.

### <a name="create-certificates-in-powershell"></a>PowerShell에서 인증서 만들기

인증서 만들기는이 흐름을 설정 하는 가장 어려운 부분입니다. `New-SelfSignedCertificate` PowerShell cmdlet을 사용 하 여 루트 인증서를 만들 수 있습니다. 인증서를 만들 때 강력한 암호를 사용 합니다. 표시 된 대로 `KeyUsageProperty` 매개 변수 및 `KeyUsage` 매개 변수를 추가 하는 것이 중요 합니다.

#### <a name="create-root-ca"></a>루트 CA 만들기

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

#### <a name="install-in-the-trusted-root"></a>신뢰할 수 있는 루트에 설치

호스트 시스템에서 루트 인증서를 신뢰할 수 있어야 합니다. 인증 기관에서 만들지 않은 루트 인증서는 기본적으로 신뢰할 수 없습니다. 다음 링크는 Windows에서이 작업을 수행 하는 방법을 설명 합니다.

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>중간 인증서

이제 루트 인증서에서 중간 인증서를 만들 수 있습니다. 이는 모든 사용 사례에는 필요 하지 않지만 많은 인증서를 만들거나 인증서 그룹을 활성화 하거나 비활성화 해야 할 수도 있습니다. `TextExtension` 매개 변수는 인증서의 기본 제약 조건에서 경로 길이를 설정 하는 데 필요 합니다.

그런 다음 Windows 호스트 시스템의 신뢰할 수 있는 중간 인증서에 중간 인증서를 추가할 수 있습니다.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>중간 인증서에서 자식 인증서 만들기

중간 인증서에서 자식 인증서를 만들 수 있습니다. 이 엔터티는 최종 엔터티 이며 더 이상 자식 인증서를 만들 필요가 없습니다.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>루트 인증서에서 자식 인증서 만들기

루트 인증서에서 직접 자식 인증서를 만들 수도 있습니다. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>예제 루트-중간 인증서-인증서

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

루트, 중간 또는 자식 인증서를 사용 하는 경우 필요에 따라 지문 또는 PublicKey를 사용 하 여 인증서의 유효성을 검사할 수 있습니다.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
