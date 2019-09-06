---
title: ASP.NET Core에서 인증서 인증 구성
author: blowdart
description: IIS 및 HTTP.SYS 용 ASP.NET Core에서 인증서 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: ce7bcdbfb8ce0f1febf34b49786e92c917be139c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384844"
---
# <a name="overview"></a><span data-ttu-id="1babd-103">개요</span><span class="sxs-lookup"><span data-stu-id="1babd-103">Overview</span></span>

<span data-ttu-id="1babd-104">`Microsoft.AspNetCore.Authentication.Certificate`ASP.NET Core에 대 한 [인증서 인증과](https://tools.ietf.org/html/rfc5246#section-7.4.4) 유사한 구현을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="1babd-105">인증서 인증은 ASP.NET Core 하기 전에는 매우 긴 TLS 수준에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="1babd-106">보다 정확 하 게,이는 인증서의 유효성을 검사 한 다음 해당 인증서를에 대해 `ClaimsPrincipal`확인할 수 있는 이벤트를 제공 하는 인증 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="1babd-107">인증서 인증을 위해 [호스트를 구성](#configure-your-host-to-require-certificates) 하 고, IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 모든 항목을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="1babd-108">프록시 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="1babd-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="1babd-109">인증서 인증은 프록시 또는 부하 분산 장치가 클라이언트와 서버 간의 트래픽을 처리 하지 않는 경우 주로 사용 되는 상태 저장 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="1babd-110">프록시 또는 부하 분산 장치를 사용 하는 경우 인증서 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="1babd-111">인증을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-111">Handles the authentication.</span></span>
* <span data-ttu-id="1babd-112">인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="1babd-113">프록시 및 부하 분산 장치를 사용 하는 환경에서 인증서 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="1babd-114">시작</span><span class="sxs-lookup"><span data-stu-id="1babd-114">Get started</span></span>

<span data-ttu-id="1babd-115">HTTPS 인증서를 획득 하 고 적용 하며 인증서를 요구 하도록 [호스트를 구성](#configure-your-host-to-require-certificates) 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="1babd-116">웹 앱에서 `Microsoft.AspNetCore.Authentication.Certificate` 패키지에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="1babd-117">그런 다음 `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` `OnCertificateValidated` 메서드에서와 함께를 호출 하 여 요청을 통해 보낸 클라이언트 인증서에 대 한 모든 보충 유효성 검사를 수행할 수 있는 대리자를 제공 합니다. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="1babd-117">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="1babd-118">해당 정보를로 `ClaimsPrincipal` 변환 하 고 `context.Principal` 속성에 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="1babd-119">인증이 실패 하는 경우이 처리기는 `403 (Forbidden)` 정상적으로 응답 `401 (Unauthorized)`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="1babd-120">초기 TLS 연결 중에 인증이 수행 되어야 한다는 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="1babd-121">처리기에 도달할 때까지 너무 늦습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="1babd-122">익명 연결에서 인증서를 사용 하는 연결로의 연결을 업그레이드할 수 있는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="1babd-123">또한 `app.UseAuthentication();` 메서드에`Startup.Configure` 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="1babd-124">그렇지 않으면 인증서에서 생성 된 것으로 `ClaimsPrincipal` 설정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="1babd-125">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="1babd-125">For example:</span></span>

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

<span data-ttu-id="1babd-126">앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="1babd-127">처리기는 일반적인 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="1babd-128">인증서 유효성 검사 구성</span><span class="sxs-lookup"><span data-stu-id="1babd-128">Configure certificate validation</span></span>

<span data-ttu-id="1babd-129">`CertificateAuthenticationOptions` 처리기에는 인증서에 대해 수행 해야 하는 최소 유효성 검사 인 몇 가지 기본 제공 유효성 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="1babd-130">이러한 각 설정은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="1babd-131">AllowedCertificateTypes = 체인, SelfSigned 또는 모두 (연결 된 | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="1babd-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="1babd-132">이 검사는 적절 한 인증서 유형만 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="1babd-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="1babd-133">ValidateCertificateUse</span></span>

<span data-ttu-id="1babd-134">이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 EKU (확장 키 사용)가 있거나 Eku가 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="1babd-135">지정 된 것 처럼 EKU가 지정 되지 않은 경우 모든 Eku가 유효한 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="1babd-136">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="1babd-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="1babd-137">이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="1babd-138">각 요청에서 처리기는 제공 된 유효한 인증서가 현재 세션 중에 만료 되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="1babd-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="1babd-139">RevocationFlag</span></span>

<span data-ttu-id="1babd-140">체인에서 해지를 확인할 인증서를 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="1babd-141">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="1babd-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="1babd-142">RevocationMode</span></span>

<span data-ttu-id="1babd-143">해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="1babd-144">인증 기관에 연결 하는 동안 오래 지연에서 온라인 확인을 지정 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="1babd-145">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="1babd-146">특정 경로에 대해서만 인증서를 요구 하도록 앱을 구성할 수 있나요?</span><span class="sxs-lookup"><span data-stu-id="1babd-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="1babd-147">이는 불가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-147">This isn't possible.</span></span> <span data-ttu-id="1babd-148">인증서 교환은 HTTPS 대화가 시작 되 면 서버에서 첫 번째 요청을 수신 하기 전에 수행 하는 것 이므로 요청 필드를 기준으로 범위를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="1babd-149">처리기 이벤트</span><span class="sxs-lookup"><span data-stu-id="1babd-149">Handler events</span></span>

<span data-ttu-id="1babd-150">처리기에는 두 가지 이벤트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-150">The handler has two events:</span></span>

* <span data-ttu-id="1babd-151">`OnAuthenticationFailed`&ndash; 인증 하는 동안 예외가 발생 하 여 반응할 수 있는 경우 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="1babd-152">`OnCertificateValidated`&ndash; 인증서의 유효성을 검사 하 고 유효성 검사를 통과 했으며 기본 보안 주체가 생성 된 후에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="1babd-153">이 이벤트를 사용 하 여 사용자 고유의 유효성 검사를 수행 하 고 보안 주체를 확대 하거나 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="1babd-154">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-154">For examples include:</span></span>
  * <span data-ttu-id="1babd-155">인증서가 서비스에 알려져 있는지 확인 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="1babd-156">사용자 고유의 보안 주체를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-156">Constructing your own principal.</span></span> <span data-ttu-id="1babd-157">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1babd-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="1babd-158">인바운드 인증서가 추가 유효성 검사를 충족 하지 않는 경우 오류 원인 `context.Fail("failure reason")` 으로를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="1babd-159">실제 기능을 사용 하려면 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="1babd-160">대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="1babd-161">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1babd-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="1babd-162">개념적으로 인증서의 유효성 검사는 권한 부여에 대 한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="1babd-163">예를 들어, 권한 부여 정책에서 발급자 또는 지문을 포함 `OnCertificateValidated`하는 것이 아니라 확인을 추가 하는 것은 완벽 하 게 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="1babd-164">인증서를 요구 하도록 호스트 구성</span><span class="sxs-lookup"><span data-stu-id="1babd-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="1babd-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="1babd-165">Kestrel</span></span>

<span data-ttu-id="1babd-166">*Program.cs*에서 다음과 같이 Kestrel을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-166">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="1babd-167">IIS</span><span class="sxs-lookup"><span data-stu-id="1babd-167">IIS</span></span>

<span data-ttu-id="1babd-168">IIS 관리자에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="1babd-169">**연결** 탭에서 사이트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="1babd-170">**기능 보기** 창에서 **SSL 설정** 옵션을 두 번 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="1babd-171">**SSL 필요** 확인란을 선택 하 고 **클라이언트 인증서** 섹션 **에서 라디오 단추를 선택** 합니다.</span><span class="sxs-lookup"><span data-stu-id="1babd-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS의 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="1babd-173">Azure 및 사용자 지정 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="1babd-173">Azure and custom web proxies</span></span>

<span data-ttu-id="1babd-174">인증서 전달 미들웨어를 구성 하는 방법은 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1babd-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
