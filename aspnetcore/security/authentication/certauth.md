---
title: ASP.NET Core에서 인증서 인증을 구성 합니다.
author: blowdart
description: ASP.NET Core에서 HTTP.sys 및 IIS에 대 한 인증서 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: barry.dorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 37567128531187bfe7dd6c9f5aa990226e70f35f
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837540"
---
# <a name="overview"></a><span data-ttu-id="a83b6-103">개요</span><span class="sxs-lookup"><span data-stu-id="a83b6-103">Overview</span></span>

<span data-ttu-id="a83b6-104">`Microsoft.AspNetCore.Authentication.Certificate` 유사한 구현을 포함 [인증서 인증](https://tools.ietf.org/html/rfc5246#section-7.4.4) ASP.NET Core에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="a83b6-105">인증서 인증 적이 ASP.NET Core 도달 하기 전에 긴 TLS 수준에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="a83b6-106">보다 정확히 말해이 인증서의 유효성을 검사 하 고 그런 다음, 해당 인증서를 해결할 수 있는 이벤트를 제공 하는 인증 처리기를 `ClaimsPrincipal`입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="a83b6-107">[호스트 구성](#configure-your-host-to-require-certificates) 인증서 인증을 위한 것 IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="a83b6-108">시작</span><span class="sxs-lookup"><span data-stu-id="a83b6-108">Get started</span></span>

<span data-ttu-id="a83b6-109">HTTPS 인증서를 획득, 적용 및 [호스트 구성](#configure-your-host-to-require-certificates) 인증서를 요구 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="a83b6-110">웹 앱에 대 한 참조를 추가 합니다 `Microsoft.AspNetCore.Authentication.Certificate` 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="a83b6-111">그런 다음 합니다 `Startup.Configure` 메서드를 호출 `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` 옵션을 사용 하 여에 대 한 대리자를 제공 하 `OnCertificateValidated` 요청과 함께 전송 된 클라이언트 인증서의 보조 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="a83b6-112">설정에 해당 정보를 `ClaimsPrincipal` 에 설정는 `context.Principal` 속성.</span><span class="sxs-lookup"><span data-stu-id="a83b6-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="a83b6-113">이 처리기가 반환 하는 경우 인증에 실패 하면를 `403 (Forbidden)` 응답 대신는 `401 (Unauthorized)`예상할 수 있듯이, 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="a83b6-114">그 이유는 인증 초기 TLS 연결 하는 동안 발생 해야 함을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="a83b6-115">처리기에 도달한 시간, 때는 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="a83b6-116">인증서를 사용 하 여 하나에 연결이 익명 연결을 통해 업그레이드 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="a83b6-117">이 밖에도 `app.UseAuthentication();` 에 `Startup.Configure` 메서드.</span><span class="sxs-lookup"><span data-stu-id="a83b6-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="a83b6-118">그렇지 않은 경우는 HttpContext.User 됩니다로 설정할 수 `ClaimsPrincipal` 인증서에서 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="a83b6-119">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a83b6-119">For example:</span></span>

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

<span data-ttu-id="a83b6-120">앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="a83b6-121">처리기를 공용 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="a83b6-122">인증서 유효성 검사 구성</span><span class="sxs-lookup"><span data-stu-id="a83b6-122">Configure certificate validation</span></span>

<span data-ttu-id="a83b6-123">`CertificateAuthenticationOptions` 처리기에는 인증서에 수행 해야 하는 최소 유효성 검사는 일부 기본 제공 유효성 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="a83b6-124">이러한 각 설정은 기본적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="a83b6-125">AllowedCertificateTypes = 자체 서명, 또는 모든 연결 (연결 | 자체 서명)</span><span class="sxs-lookup"><span data-stu-id="a83b6-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="a83b6-126">이 검사의 적절 한 인증서 형식에만 허용 됩니다 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="a83b6-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="a83b6-127">ValidateCertificateUse</span></span>

<span data-ttu-id="a83b6-128">이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 Eku 없음 또는 키 사용 (EKU) 전혀 확장을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="a83b6-129">사양 들어 eku가 없어야 지정 된 경우에 따라 다음 모든 Eku는 유효한 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="a83b6-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="a83b6-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="a83b6-131">이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="a83b6-132">각 요청에 처리기를 현재 세션에 해당 하는 동안 제공 된 경우 유효 했던 인증서 만료 되지 않았는지를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="a83b6-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="a83b6-133">RevocationFlag</span></span>

<span data-ttu-id="a83b6-134">체인의 인증서 해지 확인을 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="a83b6-135">해지 검사는 인증서는 루트 인증서에 연결 하는 경우에 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="a83b6-136">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="a83b6-136">RevocationMode</span></span>

<span data-ttu-id="a83b6-137">해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="a83b6-138">인증 기관에 연결 하는 동안 오래 지연에서 온라인 확인을 지정 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="a83b6-139">해지 검사는 인증서는 루트 인증서에 연결 하는 경우에 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="a83b6-140">특정 경로에 인증서를 요구 하도록 내 앱을 구성할 수 있나요?</span><span class="sxs-lookup"><span data-stu-id="a83b6-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="a83b6-141">이것이 불가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-141">This isn't possible.</span></span> <span data-ttu-id="a83b6-142">인증서 교환을 HTTPS 대화의 시작을 완료 되는 서버에서 모든 요청 필드를 기반으로 하는 범위에 불가능 하므로 해당 연결에서 첫 번째 요청이 수신 되기 전에 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="a83b6-143">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="a83b6-143">Handler events</span></span>

<span data-ttu-id="a83b6-144">처리기에 두 개의 이벤트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-144">The handler has two events:</span></span>

* <span data-ttu-id="a83b6-145">`OnAuthenticationFailed` &ndash; 예외가 인증 하는 동안 발생 하 고 대응할 수 하는 경우 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="a83b6-146">`OnCertificateValidated` &ndash; 인증서를 확인, 유효성 검사를 통과 하 고 기본 보안 주체를 만든 후 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="a83b6-147">이 이벤트를 사용 하면 고유한 유효성 검사를 수행 하 고 확장 하거나 보안 주체를 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="a83b6-148">예제를 보려면 다음을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-148">For examples include:</span></span>
  * <span data-ttu-id="a83b6-149">서비스에 인증서를 식별 하는 경우를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="a83b6-150">사용자 고유의 보안 주체를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-150">Constructing your own principal.</span></span> <span data-ttu-id="a83b6-151">다음 예제에서는 고려 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a83b6-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a83b6-152">인바운드 인증서 추가 유효성 검사를 충족 하지 않는 경우 호출 `context.Fail("failure reason")` 실패 이유를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="a83b6-153">실제 기능을 원할 것 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="a83b6-154">대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="a83b6-155">다음 예제에서는 고려 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a83b6-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a83b6-156">개념적으로 인증서의 유효성 검사는 권한 부여 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="a83b6-157">예를 들어, 한 검사를 추가, 발급자 또는 내부 대신 권한 부여 정책에서 지문을 `OnCertificateValidated`를 완벽 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="a83b6-158">인증서를 요구 하도록 호스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="a83b6-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a83b6-159">Kestrel</span></span>

<span data-ttu-id="a83b6-160">*Program.cs*, 아래와 같이 Kestrel을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-160">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="a83b6-161">IIS</span><span class="sxs-lookup"><span data-stu-id="a83b6-161">IIS</span></span>

<span data-ttu-id="a83b6-162">IIS 관리자에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="a83b6-163">사이트를 선택 합니다 **연결** 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="a83b6-164">두 번 클릭 합니다 **SSL 설정** 옵션을 **기능 보기** 창입니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="a83b6-165">확인 합니다 **SSL 필요** 확인란을 선택 하 고는 **필요** 라디오 단추를 **클라이언트 인증서** 섹션.</span><span class="sxs-lookup"><span data-stu-id="a83b6-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS에서 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="a83b6-167">Azure 및 사용자 지정 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="a83b6-167">Azure and custom web proxies</span></span>

<span data-ttu-id="a83b6-168">참조 된 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 미들웨어를 전달 하는 인증서를 구성 하는 방법에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="a83b6-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
