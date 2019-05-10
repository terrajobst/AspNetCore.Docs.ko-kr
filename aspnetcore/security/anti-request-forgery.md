---
title: ASP.NET Core에서 방지 교차 사이트 요청 위조 (XSRF/CSRF) 공격
author: steve-smith
description: 악성 웹 사이트는 클라이언트 브라우저와 앱 간의 상호 작용에 영향을 줄 수 있는 웹 앱에 대 한 공격을 방지 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 4498bd88e7188ea6c0063aeec054bc20e13ed5f1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086437"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="1f67a-103">ASP.NET Core에서 방지 교차 사이트 요청 위조 (XSRF/CSRF) 공격</span><span class="sxs-lookup"><span data-stu-id="1f67a-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="1f67a-104">하 여 [Steve Smith](https://ardalis.com/)하십시오 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f67a-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1f67a-105">교차 사이트 요청 위조 (XSRF 또는 CSRF, 발음 라고도 *참조 surf*)은 악의적인 웹 앱을 신뢰 하는 웹 앱을 클라이언트 브라우저 간의 상호 작용에 영향을 줄 가능해 집니다 웹에 호스팅된 앱에 대 한 공격 브라우저.</span><span class="sxs-lookup"><span data-stu-id="1f67a-105">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="1f67a-106">웹 브라우저는 웹 사이트에 몇 가지 유형의 인증 토큰이 모든 요청에 자동으로 보내기 때문에 이러한 공격 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="1f67a-107">이러한 형태의 악용이 라고도 *원클릭 공격* 또는 *세션 있는* 공격을 활용 하기 때문에 사용자의 이전 세션을 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="1f67a-108">CSRF 공격의 예:</span><span class="sxs-lookup"><span data-stu-id="1f67a-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="1f67a-109">사용자가 로그인 `www.good-banking-site.com` 폼 인증을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="1f67a-110">사용자를 인증 하 고 인증 쿠키를 포함 하는 응답을 발급 하는 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="1f67a-111">사이트는 유효한 인증 쿠키를 사용 하 여 수신 하는 모든 요청을 신뢰 하기 때문에 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="1f67a-112">사용자가 악성 사이트를 방문 `www.bad-crook-site.com`합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="1f67a-113">악성 사이트 `www.bad-crook-site.com`, 다음과 같은 HTML 폼을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="1f67a-114">양식의 `action` 악성 사이트 아니라 한 취약 한 사이트에 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="1f67a-115">CSRF의 "교차 사이트" 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="1f67a-116">사용자가 제출 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-116">The user selects the submit button.</span></span> <span data-ttu-id="1f67a-117">브라우저 요청 및 요청 된 도메인에 대 한 인증 쿠키를 자동으로 포함 `www.good-banking-site.com`합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="1f67a-118">요청에서 실행 되는 `www.good-banking-site.com` 사용자의 인증 컨텍스트를 사용 하 여 서버 인증된 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="1f67a-119">사용자가 양식을 제출 단추를 선택 하는 있는 경우 외에도 악의적인 사이트는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="1f67a-120">자동으로 폼을 전송 하는 스크립트를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="1f67a-121">폼 제출을 AJAX 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="1f67a-122">CSS를 사용 하 여 폼을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-122">Hide the form using CSS.</span></span>

<span data-ttu-id="1f67a-123">이러한 대체 시나리오에는 모든 작업 또는 악성 사이트를 처음 방문 이외의 사용자의 입력 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="1f67a-124">HTTPS를 사용 하 여 CSRF 공격을 방지 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="1f67a-125">악성 사이트를 보낼 수는 `https://www.good-banking-site.com/` 안전 하지 않은 요청을 보낼 수 것 처럼 손쉽게 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="1f67a-126">몇 가지 공격 대상 이미지 태그를 작업을 수행 하는 경우에 사용할 수 GET 요청에 응답 하는 끝점입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="1f67a-127">이 형태의 공격이 이미지를 허용 하지만 JavaScript를 차단 하는 포럼 사이트에 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="1f67a-128">변수 또는 리소스 변경는 GET 요청에서 상태를 변경 하는 앱은 악의적인 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="1f67a-129">**GET 요청 상태를 변경 하는 보호 되지 않습니다. GET 요청에서 상태를 변경 되지 않습니다 하는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="1f67a-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="1f67a-130">CSRF 공격 하기 때문에 인증 쿠키를 사용 하는 웹 앱에 대해 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="1f67a-131">브라우저는 웹 앱에서 발급 한 쿠키를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="1f67a-132">저장 된 쿠키는 인증 된 사용자에 대 한 세션 쿠키를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="1f67a-133">브라우저의 쿠키를 연결 된 모든 웹 앱에 도메인을 사용 하 여 브라우저 내에서 앱에 요청을 생성할 하는 방법에 관계 없이 모든 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="1f67a-134">하지만 CSRF 공격만 제한 되지 않습니다 쿠키를 이용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="1f67a-135">예를 들어, 기본 및 다이제스트 인증은 또한 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="1f67a-136">브라우저 세션까지 자격 증명을 자동으로 보냅니다 사용자가 기본 또는 다이제스트 인증으로 로그인 하는 것을 후&dagger; 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="1f67a-137">&dagger;이 컨텍스트에서 *세션* 사용자가 인증 되는 클라이언트 쪽 세션을 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="1f67a-138">서버 쪽 세션에 관련 되지 않은 또는 [ASP.NET Core 세션 미들웨어](xref:fundamentals/app-state)합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="1f67a-139">사용자는 예방 조치를 수행 하 여 CSRF 취약성 으로부터 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="1f67a-140">사용을 완료 하는 경우 웹 앱으로 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="1f67a-141">주기적으로 명확한 브라우저 쿠키입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="1f67a-142">그러나 CSRF 취약점은 근본적으로 웹 앱을 최종 사용자가 아니라 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="1f67a-143">인증 기본 사항</span><span class="sxs-lookup"><span data-stu-id="1f67a-143">Authentication fundamentals</span></span>

<span data-ttu-id="1f67a-144">쿠키 기반 인증은 인증의 인기 있는 폼입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="1f67a-145">토큰 기반 인증 시스템은 단일 페이지 응용 프로그램 (Spa)에 대 한 특히 널리 사용 되 고, 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="1f67a-146">쿠키 기반 인증</span><span class="sxs-lookup"><span data-stu-id="1f67a-146">Cookie-based authentication</span></span>

<span data-ttu-id="1f67a-147">사용자가 자신의 사용자 이름과 암호를 사용 하 여 인증, 인증 및 권한 부여에 사용할 수 있는 인증 티켓을 포함 하는 토큰을 발급 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="1f67a-148">모든 요청 클라이언트와 함께 제공 되는 쿠키를 사용 하면 토큰 저장.</span><span class="sxs-lookup"><span data-stu-id="1f67a-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="1f67a-149">생성 및이 쿠키 유효성 검사 쿠키 인증 미들웨어에서 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="1f67a-150">합니다 [미들웨어](xref:fundamentals/middleware/index) 암호화 된 쿠키에 사용자 보안 주체를 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="1f67a-151">후속 요청 시 미들웨어는 쿠키의 유효성을 검사, 주 서버를 다시 만듭니다 및 보안 주체를 할당 합니다 [사용자](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) 속성을 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="1f67a-152">토큰 기반 인증</span><span class="sxs-lookup"><span data-stu-id="1f67a-152">Token-based authentication</span></span>

<span data-ttu-id="1f67a-153">사용자가 인증 되 면 (없습니다 위조 방지 토큰) 토큰을 발급 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="1f67a-154">토큰의 형태로 사용자 정보를 포함 [클레임](/dotnet/framework/security/claims-based-identity-model) 또는 앱에서 유지 관리 하는 사용자 상태를 앱을 가리키는 참조 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="1f67a-155">사용자가 인증을 요구 하는 리소스에 액세스 하려고 하는 경우 토큰이 전달자 토큰의 형태로 추가 권한 부여 헤더를 사용 하 여 앱에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="1f67a-156">이렇게 하면 앱 상태 비저장입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-156">This makes the app stateless.</span></span> <span data-ttu-id="1f67a-157">각 후속 요청에서 서버 쪽 유효성 검사에 대 한 토큰 요청에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="1f67a-158">이 토큰 되지 *암호화*; 있기 *인코딩된*합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="1f67a-159">서버에서 토큰은 해당 정보에 액세스 하려면 디코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="1f67a-160">후속 요청에 토큰을 보낼 토큰은 브라우저의 로컬 저장소에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="1f67a-161">토큰은 브라우저의 로컬 저장소에 저장 된 경우 CSRF 취약성에 대해 염려 하지.</span><span class="sxs-lookup"><span data-stu-id="1f67a-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="1f67a-162">CSRF 중요 한 경우 토큰이 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="1f67a-163">하나의 도메인에서 호스팅되는 여러 앱</span><span class="sxs-lookup"><span data-stu-id="1f67a-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="1f67a-164">공유 호스팅 환경은 세션 하이재킹, CSRF, 로그인 및 다른 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="1f67a-165">하지만 `example1.contoso.net` 및 `example2.contoso.net` 호스트인 다른 호스트 간에 암시적 신뢰 관계가 없는 `*.contoso.net` 도메인입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="1f67a-166">이 암시적 트러스트 관계에는 신뢰할 수 없는 호스트를 (AJAX 요청을 제어 하는 동일 원본 정책을 반드시 HTTP 쿠키에 적용 되지 않습니다) 다른 사용자의 쿠키에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="1f67a-167">도메인을 공유 하지 않으므로 동일한 도메인에 호스트 된 앱 간에 신뢰할 수 있는 쿠키를 악용 하는 공격을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="1f67a-168">각 앱 자체 도메인에서 호스트 되는 경우 암시적 쿠키 트러스트 관계가 없는 악용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="1f67a-169">ASP.NET Core 위조 방지 구성</span><span class="sxs-lookup"><span data-stu-id="1f67a-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="1f67a-170">위조 방지를 사용 하 여 ASP.NET Core 구현 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="1f67a-171">데이터 보호 스택이 서버 팜에서 작동 하도록 구성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="1f67a-172">참조 [데이터 보호 구성](xref:security/data-protection/configuration/overview) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="1f67a-173">ASP.NET Core 2.0 이상에서는 합니다 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) HTML 폼 요소에 위조 방지 토큰을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="1f67a-174">Razor 파일의 다음 태그는 위조 방지 토큰을 자동으로 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="1f67a-175">마찬가지로 [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 양식의 메서드가 GET 되지 않으면 기본적으로 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-175">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="1f67a-176">발생 하는 HTML 폼 요소 위조 방지 토큰 자동으로 생성 때 합니다 `<form>` 태그를 포함 합니다 `method="post"` 특성 및 다음 중 하나에 해당할:</span><span class="sxs-lookup"><span data-stu-id="1f67a-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="1f67a-177">Action 특성은 빈 (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="1f67a-177">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="1f67a-178">Action 특성을 제공 하지 않으면 (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="1f67a-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="1f67a-179">HTML 폼 요소에 대 한 위조 방지 토큰 자동 생성을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="1f67a-180">명시적으로 사용 하 여 위조 방지 토큰을 사용 하지 않도록 설정 된 `asp-antiforgery` 특성:</span><span class="sxs-lookup"><span data-stu-id="1f67a-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="1f67a-181">Form 요소는 선택한 스케일 아웃 태그 도우미의 태그 도우미를 사용 하 여 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="1f67a-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="1f67a-182">제거 된 `FormTagHelper` 뷰에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="1f67a-183">`FormTagHelper` Razor 보기에는 다음 지시문을 추가 하 여 보기에서 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="1f67a-184">[Razor 페이지](xref:razor-pages/index) XSRF/CSRF에서 자동으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="1f67a-185">자세한 내용은 [XSRF/CSRF 및 Razor 페이지](xref:razor-pages/index#xsrf)합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="1f67a-186">가장 일반적인 방법은 CSRF 공격 으로부터 보호 하는 데 사용 하는 것은 *동기화 장치 토큰 패턴이* (STP).</span><span class="sxs-lookup"><span data-stu-id="1f67a-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="1f67a-187">STP은 사용자 양식 데이터를 사용 하 여 페이지를 요청할 때 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="1f67a-188">서버는 클라이언트에 현재 사용자의 id와 연결 된 토큰을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="1f67a-189">확인을 위해 서버에 클라이언트 전송 토큰 다시 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="1f67a-190">서버에서 인증 된 사용자 id와 일치 하지 않는 토큰을 받으면 요청 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="1f67a-191">토큰을 고유 하 고 예측할 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="1f67a-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="1f67a-192">일련의 요청을 적절 한 시퀀싱 되도록 토큰을 사용할 수도 있습니다 (예를 들어 요청 시퀀스를 확인 합니다. 1 페이지 &ndash; 2 페이지 &ndash; 3 페이지).</span><span class="sxs-lookup"><span data-stu-id="1f67a-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="1f67a-193">모든 ASP.NET Core MVC 및 Razor 페이지 템플릿에서 양식의 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="1f67a-194">예제 보기의 다음 쌍 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="1f67a-195">명시적으로 추가 하는 위조 방지 토큰을 `<form>` 요소는 HTML 도우미를 사용 하 여 태그 도우미를 사용 하지 않고 [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="1f67a-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="1f67a-196">위의 경우 중 각각의 ASP.NET Core에 다음과 유사한 숨겨진된 양식 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="1f67a-197">ASP.NET Core를 포함 세 [필터](xref:mvc/controllers/filters) 위조 방지 토큰을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="1f67a-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="1f67a-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="1f67a-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="1f67a-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="1f67a-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="1f67a-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="1f67a-201">위조 방지 옵션</span><span class="sxs-lookup"><span data-stu-id="1f67a-201">Antiforgery options</span></span>

<span data-ttu-id="1f67a-202">사용자 지정 [위조 방지 옵션](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) 에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f67a-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="1f67a-203">&dagger;antiforgery 설정 `Cookie` 의 속성을 사용 하 여 속성을 [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="1f67a-204">옵션</span><span class="sxs-lookup"><span data-stu-id="1f67a-204">Option</span></span> | <span data-ttu-id="1f67a-205">설명</span><span class="sxs-lookup"><span data-stu-id="1f67a-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="1f67a-206">쿠키</span><span class="sxs-lookup"><span data-stu-id="1f67a-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="1f67a-207">위조 방지 쿠키를 만드는 데 설정을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="1f67a-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="1f67a-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="1f67a-209">위조 방지 토큰 보기에서 렌더링할 위조 방지 시스템에서 사용 하는 숨겨진된 폼 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="1f67a-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="1f67a-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="1f67a-211">위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="1f67a-212">경우 `null`, 시스템 데이터 형식에만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="1f67a-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="1f67a-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="1f67a-214">생성을 보류할 지 여부를 지정 된 `X-Frame-Options` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="1f67a-215">기본적으로 헤더는 "SAMEORIGIN"의 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="1f67a-216">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-216">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="1f67a-217">옵션</span><span class="sxs-lookup"><span data-stu-id="1f67a-217">Option</span></span> | <span data-ttu-id="1f67a-218">설명</span><span class="sxs-lookup"><span data-stu-id="1f67a-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="1f67a-219">쿠키</span><span class="sxs-lookup"><span data-stu-id="1f67a-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="1f67a-220">위조 방지 쿠키를 만드는 데 설정을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="1f67a-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="1f67a-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="1f67a-222">쿠키의 도메인입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-222">The domain of the cookie.</span></span> <span data-ttu-id="1f67a-223">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-223">Defaults to `null`.</span></span> <span data-ttu-id="1f67a-224">이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1f67a-225">권장 되는 대안은 Cookie.Domain을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="1f67a-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="1f67a-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="1f67a-227">쿠키의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-227">The name of the cookie.</span></span> <span data-ttu-id="1f67a-228">설정 되지 않은 시스템 생성 고유 이름이 합니다 [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery입니다. ")입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="1f67a-229">이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1f67a-230">권장 되는 대안은 Cookie.Name을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="1f67a-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="1f67a-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="1f67a-232">쿠키에 설정 된 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-232">The path set on the cookie.</span></span> <span data-ttu-id="1f67a-233">이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1f67a-234">권장 되는 대안은 Cookie.Path을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="1f67a-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="1f67a-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="1f67a-236">위조 방지 토큰 보기에서 렌더링할 위조 방지 시스템에서 사용 하는 숨겨진된 폼 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="1f67a-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="1f67a-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="1f67a-238">위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="1f67a-239">경우 `null`, 시스템 데이터 형식에만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="1f67a-240">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="1f67a-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="1f67a-241">위조 방지 시스템에서 HTTPS가 필요한 지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="1f67a-242">경우 `true`, HTTPS가 아닌 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="1f67a-243">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-243">Defaults to `false`.</span></span> <span data-ttu-id="1f67a-244">이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1f67a-245">권장된 대안 Cookie.SecurePolicy를 설정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="1f67a-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="1f67a-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="1f67a-247">생성을 보류할 지 여부를 지정 된 `X-Frame-Options` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="1f67a-248">기본적으로 헤더는 "SAMEORIGIN"의 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="1f67a-249">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="1f67a-250">자세한 내용은 [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="1f67a-251">IAntiforgery 된 위조 방지 기능을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="1f67a-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 위조 방지 기능을 구성 하는 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="1f67a-253">`IAntiforgery` 요청할 수 있습니다 합니다 `Configure` 메서드는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="1f67a-254">다음 예제에서는 위조 방지 토큰을 생성 하 고 (기본 Angular 명명 규칙이이 항목의 뒷부분에 설명 된 사용)를 쿠키로이 응답에 보내는 앱의 홈 페이지에서 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="1f67a-255">위조 방지 유효성 검사 필요</span><span class="sxs-lookup"><span data-stu-id="1f67a-255">Require antiforgery validation</span></span>

<span data-ttu-id="1f67a-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 은 개별 작업, 컨트롤러에 적용할 수 있는 작업 필터 또는 전역적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="1f67a-257">요청에 유효한 위조 방지 토큰을 포함 하지 않는 한이 필터가 적용 하는 작업에 대 한 요청이 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="1f67a-258">`ValidateAntiForgeryToken` 특성에 대 한 요청 작업 메서드에 HTTP GET 요청을 포함 하 여 데코레이팅되 토큰이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="1f67a-259">경우는 `ValidateAntiForgeryToken` 앱의 컨트롤러에서 특성 적용, 사용 하 여 재정의할 수 있습니다는 `IgnoreAntiforgeryToken` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="1f67a-260">ASP.NET Core는 GET 요청을 위조 방지 토큰을 자동으로 추가 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="1f67a-261">자동으로 안전 하지 않은 HTTP 메서드에 대 한 위조 방지 토큰 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="1f67a-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="1f67a-262">ASP.NET Core 앱 안전한 HTTP 메서드 (GET, HEAD, 옵션 및 추적)에 대 한 위조 방지 토큰을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="1f67a-263">광범위 하 게 적용 하는 대신를 `ValidateAntiForgeryToken` 특성을 사용 하 여 재정의 한 다음 `IgnoreAntiforgeryToken` 특성을 [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="1f67a-264">이 특성은 동일 하 게 작동 합니다 `ValidateAntiForgeryToken` 특성을 제외 하 고 다음 HTTP 메서드를 사용 하 여 요청에 대 한 토큰 필요가:</span><span class="sxs-lookup"><span data-stu-id="1f67a-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="1f67a-265">가져오기</span><span class="sxs-lookup"><span data-stu-id="1f67a-265">GET</span></span>
* <span data-ttu-id="1f67a-266">HEAD</span><span class="sxs-lookup"><span data-stu-id="1f67a-266">HEAD</span></span>
* <span data-ttu-id="1f67a-267">옵션</span><span class="sxs-lookup"><span data-stu-id="1f67a-267">OPTIONS</span></span>
* <span data-ttu-id="1f67a-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="1f67a-268">TRACE</span></span>

<span data-ttu-id="1f67a-269">사용 좋습니다 `AutoValidateAntiforgeryToken` 아닌 API 시나리오에 대 한 광범위 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="1f67a-270">이렇게 하면 POST 작업은 기본적으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="1f67a-271">기본적으로 위조 방지 토큰을 무시 하지 않는 한 `ValidateAntiForgeryToken` 개별 작업 메서드에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="1f67a-272">이 가능성이을 POST 작업 메서드에 대 한이 시나리오에서는 보호 되지 않은 실수로 CSRF 공격에 취약 한 해당 앱을 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="1f67a-273">모든 게시물 위조 방지 토큰을 전송 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="1f67a-274">Api 토큰의 쿠키 부분을 보내기 위한 자동 메커니즘이 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="1f67a-275">아마도 구현 클라이언트 코드 구현에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="1f67a-276">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-276">Some examples are shown below:</span></span>

<span data-ttu-id="1f67a-277">클래스 수준 예제:</span><span class="sxs-lookup"><span data-stu-id="1f67a-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="1f67a-278">전역 예제:</span><span class="sxs-lookup"><span data-stu-id="1f67a-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="1f67a-279">전역 재정의 또는 컨트롤러 위조 방지 특성</span><span class="sxs-lookup"><span data-stu-id="1f67a-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="1f67a-280">합니다 [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) 필터는 위조 방지 토큰을 지정 된 작업 (또는 컨트롤러)에 대 한 필요성을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="1f67a-281">이 필터 재정의 적용 하면 `ValidateAntiForgeryToken` 고 `AutoValidateAntiforgeryToken` (전역적으로 또는 컨트롤러)는 더 높은 수준에서 지정 된 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="1f67a-282">인증 후 새로 고침 토큰</span><span class="sxs-lookup"><span data-stu-id="1f67a-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="1f67a-283">뷰 또는 Razor 페이지에 사용자를 리디렉션하여 사용자가 인증 한 후 토큰 새로 고쳐야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="1f67a-284">JavaScript, AJAX 및 Spa</span><span class="sxs-lookup"><span data-stu-id="1f67a-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="1f67a-285">전통적인 HTML 기반 앱에서 위조 방지 토큰은 숨겨진된 양식 필드를 사용 하 여 서버에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="1f67a-286">최신 JavaScript 기반 응용 프로그램 및 Spa에서 많은 요청이 프로그래밍 방식으로 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="1f67a-287">이러한 AJAX 요청 토큰을 보낼 다른 기술 (예: 요청 헤더 또는 쿠키)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="1f67a-288">쿠키 인증 토큰을 저장 하 고 서버의 API 요청을 인증 하기를 사용 하는 경우 CSRF 잠재적인 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="1f67a-289">로컬 저장소를 사용 하 여 토큰을 저장, 하므로 로컬 저장소에서 값은 모든 요청을 사용 하 여 서버에 자동으로 전송 되지 않습니다 CSRF 취약점으로 인 한 완화 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="1f67a-290">따라서 로컬 저장소를 사용 하 여 위조 방지 토큰을 요청 헤더는 권장된 방법으로 토큰을 보내는 클라이언트에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="1f67a-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="1f67a-291">JavaScript</span></span>

<span data-ttu-id="1f67a-292">JavaScript를 사용 하 여 뷰를 사용 하 여, 토큰을 만들 수 있습니다 뷰 내에서 서비스를 사용 하 여.</span><span class="sxs-lookup"><span data-stu-id="1f67a-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="1f67a-293">삽입 된 [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 보기 및 호출 서비스 [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="1f67a-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="1f67a-294">이 방법은 서버에서 쿠키를 설정 하거나 클라이언트에서 읽기를 직접 처리 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="1f67a-295">앞의 예제 JavaScript를 사용 하 여 AJAX POST 헤더에 대 한 숨겨진된 필드 값을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="1f67a-296">또한 JavaScript 쿠키에서 토큰에 액세스 하 고 쿠키의 콘텐츠를 사용 하 여 토큰의 값을 사용 하 여 헤더를 생성 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="1f67a-297">라고 하는 헤더에서 토큰을 보내도록 요청 스크립트 가정 `X-CSRF-TOKEN`를 검색할 위조 방지 서비스를 구성 합니다 `X-CSRF-TOKEN` 헤더:</span><span class="sxs-lookup"><span data-stu-id="1f67a-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="1f67a-298">다음 예제에서는 JavaScript를 사용 하 여 적절 한 헤더를 사용 하 여 AJAX 요청을 확인 하십시오.</span><span class="sxs-lookup"><span data-stu-id="1f67a-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="1f67a-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="1f67a-299">AngularJS</span></span>

<span data-ttu-id="1f67a-300">AngularJS는 CSRF 주소로 규칙을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="1f67a-301">서버 이름의 쿠키를 보내면 `XSRF-TOKEN`, AngularJS `$http` 서비스 요청을 서버로 보낼 때 헤더에 쿠키 값을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="1f67a-302">이 프로세스는 자동입니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-302">This process is automatic.</span></span> <span data-ttu-id="1f67a-303">헤더를 클라이언트에서 명시적으로 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="1f67a-304">헤더 이름은 `X-XSRF-TOKEN`합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="1f67a-305">서버는이 헤더를 검색 하 고 해당 내용의 유효성을 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="1f67a-306">응용 프로그램 시작에이 규칙을 사용 하 여 작업을 ASP.NET Core api:</span><span class="sxs-lookup"><span data-stu-id="1f67a-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="1f67a-307">앱을 구성 하 라는 쿠키에서 토큰을 제공할 `XSRF-TOKEN`합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="1f67a-308">라는 헤더에 대 한 검색할 위조 방지 서비스 구성 `X-XSRF-TOKEN`합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="1f67a-309">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f67a-309">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="1f67a-310">Antiforgery 확장</span><span class="sxs-lookup"><span data-stu-id="1f67a-310">Extend antiforgery</span></span>

<span data-ttu-id="1f67a-311">합니다 [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식 개발자가 각 토큰의 추가 데이터를 왕복 하 여 CSRF 방지 시스템의 동작을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="1f67a-312">[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 때마다 메서드는 필드 토큰 생성 되 고 반환 값은 생성 된 토큰에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="1f67a-313">구현 자가 없습니다 타임 스탬프, nonce, 또는 다른 값을 반환 하 고 호출 하 [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 토큰 유효성을 검사할 때이 데이터를 유효성 검사를 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="1f67a-314">클라이언트의 사용자 이름 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="1f67a-315">했지만 추가 데이터 토큰에 포함 된 경우 `IAntiForgeryAdditionalDataProvider` 는 구성 추가 데이터 확인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f67a-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f67a-316">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1f67a-316">Additional resources</span></span>

* <span data-ttu-id="1f67a-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 대 [Web Application Security Project를 열고](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="1f67a-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
