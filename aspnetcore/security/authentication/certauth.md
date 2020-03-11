---
title: ASP.NET Core에서 인증서 인증 구성
author: blowdart
description: IIS 및 HTTP.SYS 용 ASP.NET Core에서 인증서 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 280daa86510d4445c791b6952653122961f13aeb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654171"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="c4e6a-103">ASP.NET Core에서 인증서 인증 구성</span><span class="sxs-lookup"><span data-stu-id="c4e6a-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="c4e6a-104">`Microsoft.AspNetCore.Authentication.Certificate`에는 ASP.NET Core에 대 한 [인증서 인증과](https://tools.ietf.org/html/rfc5246#section-7.4.4) 비슷한 구현이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="c4e6a-105">인증서 인증은 ASP.NET Core 하기 전에는 매우 긴 TLS 수준에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="c4e6a-106">보다 정확 하 게,이는 인증서의 유효성을 검사 한 다음 해당 인증서를 `ClaimsPrincipal`에 대해 확인할 수 있는 이벤트를 제공 하는 인증 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="c4e6a-107">인증서 인증을 위해 [호스트를 구성](#configure-your-host-to-require-certificates) 하 고, IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 모든 항목을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="c4e6a-108">프록시 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="c4e6a-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="c4e6a-109">인증서 인증은 프록시 또는 부하 분산 장치가 클라이언트와 서버 간의 트래픽을 처리 하지 않는 경우 주로 사용 되는 상태 저장 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="c4e6a-110">프록시 또는 부하 분산 장치를 사용 하는 경우 인증서 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="c4e6a-111">인증을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-111">Handles the authentication.</span></span>
* <span data-ttu-id="c4e6a-112">인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="c4e6a-113">프록시 및 부하 분산 장치를 사용 하는 환경에서 인증서 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="c4e6a-114">시작하기</span><span class="sxs-lookup"><span data-stu-id="c4e6a-114">Get started</span></span>

<span data-ttu-id="c4e6a-115">HTTPS 인증서를 획득 하 고 적용 하며 인증서를 요구 하도록 [호스트를 구성](#configure-your-host-to-require-certificates) 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="c4e6a-116">웹 앱에서 `Microsoft.AspNetCore.Authentication.Certificate` 패키지에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="c4e6a-117">그런 다음 `Startup.ConfigureServices` 메서드에서 옵션과 함께 `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);`를 호출 하 여 요청과 함께 전송 된 클라이언트 인증서에 대 한 모든 보충 유효성 검사를 수행할 수 있는 `OnCertificateValidated` 대리자를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="c4e6a-118">해당 정보를 `ClaimsPrincipal` 설정 하 고 `context.Principal` 속성에 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="c4e6a-119">인증이 실패 하는 경우이 처리기는 `401 (Unauthorized)``403 (Forbidden)` 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="c4e6a-120">초기 TLS 연결 중에 인증이 수행 되어야 한다는 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="c4e6a-121">처리기에 도달할 때까지 너무 늦습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="c4e6a-122">익명 연결에서 인증서를 사용 하는 연결로의 연결을 업그레이드할 수 있는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="c4e6a-123">또한 `Startup.Configure` 메서드에 `app.UseAuthentication();`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="c4e6a-124">그렇지 않으면 `HttpContext.User` 인증서에서 만든 `ClaimsPrincipal`로 설정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="c4e6a-125">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-125">For example:</span></span>

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

<span data-ttu-id="c4e6a-126">앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="c4e6a-127">처리기는 일반적인 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="c4e6a-128">인증서 유효성 검사 구성</span><span class="sxs-lookup"><span data-stu-id="c4e6a-128">Configure certificate validation</span></span>

<span data-ttu-id="c4e6a-129">`CertificateAuthenticationOptions` 처리기에는 인증서에 대해 수행 해야 하는 최소한의 유효성 검사를 수행 하는 몇 가지 기본 제공 유효성 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="c4e6a-130">이러한 각 설정은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="c4e6a-131">AllowedCertificateTypes = 체인, SelfSigned 또는 모두 (연결 된 | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="c4e6a-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="c4e6a-132">기본값: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="c4e6a-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="c4e6a-133">이 검사는 적절 한 인증서 유형만 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="c4e6a-134">앱에서 자체 서명 된 인증서를 사용 하는 경우이 옵션을 `CertificateTypes.All` 또는 `CertificateTypes.SelfSigned`으로 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="c4e6a-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="c4e6a-135">ValidateCertificateUse</span></span>

<span data-ttu-id="c4e6a-136">기본값: `true`</span><span class="sxs-lookup"><span data-stu-id="c4e6a-136">Default value: `true`</span></span>

<span data-ttu-id="c4e6a-137">이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 EKU (확장 키 사용)가 있거나 Eku가 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="c4e6a-138">지정 된 것 처럼 EKU가 지정 되지 않은 경우 모든 Eku가 유효한 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="c4e6a-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="c4e6a-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="c4e6a-140">기본값: `true`</span><span class="sxs-lookup"><span data-stu-id="c4e6a-140">Default value: `true`</span></span>

<span data-ttu-id="c4e6a-141">이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="c4e6a-142">각 요청에서 처리기는 제공 된 유효한 인증서가 현재 세션 중에 만료 되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="c4e6a-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="c4e6a-143">RevocationFlag</span></span>

<span data-ttu-id="c4e6a-144">기본값: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="c4e6a-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="c4e6a-145">체인에서 해지를 확인할 인증서를 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="c4e6a-146">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="c4e6a-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="c4e6a-147">RevocationMode</span></span>

<span data-ttu-id="c4e6a-148">기본값: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="c4e6a-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="c4e6a-149">해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="c4e6a-150">인증 기관에 연결 하는 동안 오래 지연에서 온라인 확인을 지정 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="c4e6a-151">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="c4e6a-152">특정 경로에 대해서만 인증서를 요구 하도록 앱을 구성할 수 있나요?</span><span class="sxs-lookup"><span data-stu-id="c4e6a-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="c4e6a-153">이는 불가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-153">This isn't possible.</span></span> <span data-ttu-id="c4e6a-154">인증서 교환은 HTTPS 대화가 시작 되 면 서버에서 첫 번째 요청을 수신 하기 전에 수행 하는 것 이므로 요청 필드를 기준으로 범위를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="c4e6a-155">처리기 이벤트</span><span class="sxs-lookup"><span data-stu-id="c4e6a-155">Handler events</span></span>

<span data-ttu-id="c4e6a-156">처리기에는 두 가지 이벤트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-156">The handler has two events:</span></span>

* <span data-ttu-id="c4e6a-157">인증 하는 동안 예외가 발생 하 고 대응할 수 있는 경우 `OnAuthenticationFailed` &ndash; 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-157">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="c4e6a-158">인증서의 유효성을 검사 하 고 유효성 검사를 통과 하 여 기본 보안 주체를 만든 후에 호출 &ndash; `OnCertificateValidated`.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-158">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="c4e6a-159">이 이벤트를 사용 하 여 사용자 고유의 유효성 검사를 수행 하 고 보안 주체를 확대 하거나 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="c4e6a-160">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-160">For examples include:</span></span>
  * <span data-ttu-id="c4e6a-161">인증서가 서비스에 알려져 있는지 확인 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="c4e6a-162">사용자 고유의 보안 주체를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-162">Constructing your own principal.</span></span> <span data-ttu-id="c4e6a-163">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="c4e6a-164">인바운드 인증서가 추가 유효성 검사를 충족 하지 않는 경우 실패 이유를 사용 하 여 `context.Fail("failure reason")`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="c4e6a-165">실제 기능을 사용 하려면 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="c4e6a-166">대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="c4e6a-167">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="c4e6a-168">개념적으로 인증서의 유효성 검사는 권한 부여에 대 한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="c4e6a-169">예를 들어 `OnCertificateValidated`에 포함 되지 않고 권한 부여 정책의 발급자 또는 지문을 추가 하는 것은 완벽 하 게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="c4e6a-170">인증서를 요구 하도록 호스트 구성</span><span class="sxs-lookup"><span data-stu-id="c4e6a-170">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="c4e6a-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c4e6a-171">Kestrel</span></span>

<span data-ttu-id="c4e6a-172">*Program.cs*에서 다음과 같이 Kestrel을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-172">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="c4e6a-173"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="c4e6a-174">IIS</span><span class="sxs-lookup"><span data-stu-id="c4e6a-174">IIS</span></span>

<span data-ttu-id="c4e6a-175">IIS 관리자에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="c4e6a-176">**연결** 탭에서 사이트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="c4e6a-177">**기능 보기** 창에서 **SSL 설정** 옵션을 두 번 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="c4e6a-178">**SSL 필요** 확인란을 선택 하 고 **클라이언트 인증서** 섹션 **에서 라디오 단추를 선택** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS의 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="c4e6a-180">Azure 및 사용자 지정 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="c4e6a-180">Azure and custom web proxies</span></span>

<span data-ttu-id="c4e6a-181">인증서 전달 미들웨어를 구성 하는 방법은 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="c4e6a-182">Azure Web Apps에서 인증서 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c4e6a-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="c4e6a-183">Azure에는 전달 구성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="c4e6a-184">인증서 전달 미들웨어에 이미 설치 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="c4e6a-185">이렇게 하려면 CertificateForwardingMiddleware이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="c4e6a-186">사용자 지정 웹 프록시에서 인증서 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c4e6a-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="c4e6a-187">`AddCertificateForwarding` 메서드는 다음을 지정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="c4e6a-188">클라이언트 헤더 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-188">The client header name.</span></span>
* <span data-ttu-id="c4e6a-189">`HeaderConverter` 속성을 사용 하 여 인증서를 로드 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="c4e6a-190">사용자 지정 웹 프록시에서 인증서는 사용자 지정 요청 헤더로 전달 됩니다 (예: `X-SSL-CERT`).</span><span class="sxs-lookup"><span data-stu-id="c4e6a-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="c4e6a-191">이를 사용 하려면 `Startup.ConfigureServices`에서 인증서 전달을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
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

<span data-ttu-id="c4e6a-192">그러면 `Startup.Configure` 메서드가 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="c4e6a-193">`UseCertificateForwarding`는 `UseAuthentication`를 호출 하기 전에 호출 되며 `UseAuthorization`됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="c4e6a-194">별도의 클래스를 사용 하 여 유효성 검사 논리를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="c4e6a-195">이 예제에서는 동일한 자체 서명 된 인증서를 사용 하기 때문에 인증서만 사용할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="c4e6a-196">클라이언트 인증서와 서버 인증서의 지문이 일치 하는지 확인 합니다. 그렇지 않으면 인증서를 사용할 수 있으며 인증 하는 데 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="c4e6a-197">이는 `AddCertificate` 메서드 내에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="c4e6a-198">중간 또는 자식 인증서를 사용 하는 경우 여기에서 주체 또는 발급자의 유효성을 검사할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="c4e6a-199">Certificate 및 HttpClientHandler를 사용 하 여 HttpClient 구현</span><span class="sxs-lookup"><span data-stu-id="c4e6a-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="c4e6a-200">HttpClientHandler는 HttpClient 클래스의 생성자에 직접 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-200">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="c4e6a-201">HttpClient의 인스턴스를 만들 때 주의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-201">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="c4e6a-202">그런 다음 HttpClient는 각 요청과 함께 인증서를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-202">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="c4e6a-203">IHttpClientFactory에서 인증서 및 명명 된 HttpClient를 사용 하 여 HttpClient 구현</span><span class="sxs-lookup"><span data-stu-id="c4e6a-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="c4e6a-204">다음 예제에서는 처리기의 ClientCertificates 속성을 사용 하 여 클라이언트 인증서가 HttpClientHandler에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-204">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="c4e6a-205">그런 다음 ConfigurePrimaryHttpMessageHandler 메서드를 사용 하 여이 처리기를 HttpClient의 명명 된 인스턴스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-205">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="c4e6a-206">이는 ConfigureServices 메서드의 Startup 클래스에서 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-206">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="c4e6a-207">그런 다음 IHttpClientFactory를 사용 하 여 처리기와 인증서를 사용 하 여 명명 된 인스턴스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-207">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="c4e6a-208">Startup 클래스에 정의 된 클라이언트 이름을 가진 CreateClient 메서드는 인스턴스를 가져오는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-208">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="c4e6a-209">필요에 따라 클라이언트를 사용 하 여 HTTP 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="c4e6a-210">서버에 올바른 인증서를 보내면 데이터가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="c4e6a-211">인증서 나 잘못 된 인증서를 보낸 경우 HTTP 403 상태 코드가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="c4e6a-212">PowerShell에서 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="c4e6a-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="c4e6a-213">인증서 만들기는이 흐름을 설정 하는 가장 어려운 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="c4e6a-214">`New-SelfSignedCertificate` PowerShell cmdlet을 사용 하 여 루트 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="c4e6a-215">인증서를 만들 때 강력한 암호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="c4e6a-216">표시 된 대로 `KeyUsageProperty` 매개 변수 및 `KeyUsage` 매개 변수를 추가 하는 것이 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="c4e6a-217">루트 CA 만들기</span><span class="sxs-lookup"><span data-stu-id="c4e6a-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="c4e6a-218">`-DnsName` 매개 변수 값은 앱의 배포 대상과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="c4e6a-219">예를 들어 개발용 "localhost"입니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="c4e6a-220">신뢰할 수 있는 루트에 설치</span><span class="sxs-lookup"><span data-stu-id="c4e6a-220">Install in the trusted root</span></span>

<span data-ttu-id="c4e6a-221">호스트 시스템에서 루트 인증서를 신뢰할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="c4e6a-222">인증 기관에서 만들지 않은 루트 인증서는 기본적으로 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="c4e6a-223">다음 링크는 Windows에서이 작업을 수행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="c4e6a-224">중간 인증서</span><span class="sxs-lookup"><span data-stu-id="c4e6a-224">Intermediate certificate</span></span>

<span data-ttu-id="c4e6a-225">이제 루트 인증서에서 중간 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="c4e6a-226">이는 모든 사용 사례에는 필요 하지 않지만 많은 인증서를 만들거나 인증서 그룹을 활성화 하거나 비활성화 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="c4e6a-227">`TextExtension` 매개 변수는 인증서의 기본 제약 조건에서 경로 길이를 설정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="c4e6a-228">그런 다음 Windows 호스트 시스템의 신뢰할 수 있는 중간 인증서에 중간 인증서를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="c4e6a-229">중간 인증서에서 자식 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="c4e6a-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="c4e6a-230">중간 인증서에서 자식 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="c4e6a-231">이 엔터티는 최종 엔터티 이며 더 이상 자식 인증서를 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="c4e6a-232">루트 인증서에서 자식 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="c4e6a-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="c4e6a-233">루트 인증서에서 직접 자식 인증서를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="c4e6a-234">예제 루트-중간 인증서-인증서</span><span class="sxs-lookup"><span data-stu-id="c4e6a-234">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="c4e6a-235">루트, 중간 또는 자식 인증서를 사용 하는 경우 필요에 따라 지문 또는 PublicKey를 사용 하 여 인증서의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4e6a-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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
