---
title: ASP.NET Core에서 HTTPS 적용
author: rick-anderson
description: ASP.NET Core 웹 앱에 HTTPS/TLS를 요구 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 08ce50775d1b5348cb0528a1724cec2e5c72dae2
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152901"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="11185-103">ASP.NET Core에서 HTTPS 적용</span><span class="sxs-lookup"><span data-stu-id="11185-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="11185-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="11185-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="11185-105">이 문서에서는 다음과 같은 내용을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="11185-105">This document shows how to:</span></span>

* <span data-ttu-id="11185-106">모든 요청에 대 한 HTTPS가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="11185-107">모든 HTTP 요청을 HTTPS로 리디렉션하는 방법.</span><span class="sxs-lookup"><span data-stu-id="11185-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="11185-108">API가 없습니다. 첫 번째 요청 시 중요 한 데이터를 보낸 클라이언트를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="11185-109">API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="11185-109">API projects</span></span>
>
> <span data-ttu-id="11185-110">수행할 **되지** 사용 하 여 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 중요 한 정보를 수신 하는 Web Api에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="11185-111">`RequireHttpsAttribute` 브라우저는 HTTP에서 HTTPS로 리디렉션하 HTTP 상태 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="11185-112">API 클라이언트 이해 하지 못하거나 HTTP에서 HTTPS로 리디렉션 준수 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="11185-113">이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="11185-114">Web Api을 수행 해야합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="11185-115">HTTP에서 수신 대기할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="11185-116">상태 코드 400 (잘못 된 요청)를 사용 하 여 연결을 닫고 요청을 제공 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="11185-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="11185-117">API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="11185-117">API projects</span></span>
>
> <span data-ttu-id="11185-118">수행할 **되지** 사용 하 여 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 중요 한 정보를 수신 하는 Web Api에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-118">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="11185-119">`RequireHttpsAttribute` 브라우저는 HTTP에서 HTTPS로 리디렉션하 HTTP 상태 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-119">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="11185-120">API 클라이언트 이해 하지 못하거나 HTTP에서 HTTPS로 리디렉션 준수 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-120">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="11185-121">이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-121">Such clients may send information over HTTP.</span></span> <span data-ttu-id="11185-122">Web Api을 수행 해야합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-122">Web APIs should either:</span></span>
>
> * <span data-ttu-id="11185-123">HTTP에서 수신 대기할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-123">Not listen on HTTP.</span></span>
> * <span data-ttu-id="11185-124">상태 코드 400 (잘못 된 요청)를 사용 하 여 연결을 닫고 요청을 제공 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="11185-124">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="11185-125">HSTS 및 API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="11185-125">HSTS and API projects</span></span>
>
> <span data-ttu-id="11185-126">기본 API 프로젝트를 포함 하지 마세요 [HSTS](#hsts) HSTS는 일반적으로 브라우저 유일한 명령 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-126">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="11185-127">휴대폰 또는 데스크톱 앱의 경우와 같은 다른 호출자 마십시오 **되지** 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="11185-127">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="11185-128">브라우저 내 에서도 단일 HTTP 통해 API에 인증 된 호출에 위험 안전 하지 않은 네트워크.</span><span class="sxs-lookup"><span data-stu-id="11185-128">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="11185-129">만 수신 하 고 HTTPS를 통해 응답 API 프로젝트를 구성 하는 보안 방법이입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-129">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="11185-130">HTTPS 필요</span><span class="sxs-lookup"><span data-stu-id="11185-130">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="11185-131">ASP.NET Core는 프로덕션 웹 앱 호출을 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-131">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="11185-132">HTTPS 리디렉션을 미들웨어 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="11185-133">HSTS 미들웨어 ([UseHsts](#http-strict-transport-security-protocol-hsts)) 클라이언트로 HTTP 엄격한 전송 보안 프로토콜 (HSTS) 헤더를 보내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="11185-134">역방향 프록시 구성에 배포 된 앱 프록시 연결 보안 (HTTPS)을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="11185-135">프록시는 또한 HTTPS 간의 리디렉션으로 처리, 경우 HTTPS 리디렉션을 미들웨어를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="11185-136">프록시 서버도 HSTS 헤더를 작성을 처리 하는 경우 (예를 들어 [이상에서 IIS 10.0 (1709) 네이티브 HSTS 지원](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS 미들웨어는 앱에서 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="11185-137">자세한 내용은 [옵트 아웃 HTTPS/HSTS의 프로젝트 생성 시](#opt-out-of-httpshsts-on-project-creation)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="11185-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="11185-138">UseHttpsRedirection</span></span>

<span data-ttu-id="11185-139">다음 코드 호출 `UseHttpsRedirection` 에 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="11185-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="11185-140">위의 강조 표시 된 코드:</span><span class="sxs-lookup"><span data-stu-id="11185-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="11185-141">기본값을 사용 하 여 [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="11185-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="11185-142">기본값을 사용 하 여 [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) 의해 재정의 되지 않는 (null)은 `ASPNETCORE_HTTPS_PORT` 환경 변수 또는 [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="11185-143">영구 리디렉션 보다는 임시 리디렉션을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="11185-144">링크 캐싱을 개발 환경에서 불안정 한 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="11185-145">비 개발 환경에서 앱이 영구 리디렉션 상태 코드를 전송 하려는 경우 참조를 [프로덕션 환경에서 영구적인 리디렉션을 구성](#configure-permanent-redirects-in-production) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="11185-146">사용 하는 것이 좋습니다 [HSTS](#http-strict-transport-security-protocol-hsts) 만 리소스를 보호 하는 클라이언트에 알릴 수 요청 (프로덕션)에 앱에 전송 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="11185-147">포트 구성</span><span class="sxs-lookup"><span data-stu-id="11185-147">Port configuration</span></span>

<span data-ttu-id="11185-148">포트를 안전 하지 않은 요청을 HTTPS로 리디렉션하는 미들웨어에 대 한 사용할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="11185-149">사용 가능한 포트가 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="11185-149">If no port is available:</span></span>

* <span data-ttu-id="11185-150">HTTPS로 리디렉션 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="11185-151">미들웨어 "리디렉션에 대 한 https 포트를 확인 하지 못했습니다." 경고를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="11185-152">다음 방법 중 하나를 사용 하 여 HTTPS 포트를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="11185-153">설정할 [HttpsRedirectionOptions.HttpsPort](#options)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="11185-154">설정 된 `ASPNETCORE_HTTPS_PORT` 환경 변수 또는 [https_port 웹 호스트 구성 설정을](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="11185-154">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="11185-155">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="11185-155">**Key**: `https_port`</span></span>  
  <span data-ttu-id="11185-156">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="11185-156">**Type**: *string*</span></span>  
  <span data-ttu-id="11185-157">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-157">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="11185-158">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="11185-158">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="11185-159">**환경 변수**: `<PREFIX_>HTTPS_PORT` (접두사 `ASPNETCORE_` 사용 하는 경우는 [웹 호스트](xref:fundamentals/host/web-host).)</span><span class="sxs-lookup"><span data-stu-id="11185-159">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="11185-160">구성 하는 경우는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 에서 `Program`:</span><span class="sxs-lookup"><span data-stu-id="11185-160">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="11185-161">보안 체계를 사용 하 여 포트를 표시 합니다 `ASPNETCORE_URLS` 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-161">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="11185-162">환경 변수는 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-162">The environment variable configures the server.</span></span> <span data-ttu-id="11185-163">미들웨어는 HTTPS 포트를 통해 직접 검색 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-163">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="11185-164">역방향 프록시 배포에이 방법은 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-164">This approach doesn't work in reverse proxy deployments.</span></span>
* <span data-ttu-id="11185-165">개발에서에 HTTPS URL을 설정 *launchsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-165">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="11185-166">IIS Express를 사용 하는 경우에 HTTPS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-166">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="11185-167">공용 edge 배포에 대 한 HTTPS URL 끝점을 구성 [Kestrel](xref:fundamentals/servers/kestrel) 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 서버.</span><span class="sxs-lookup"><span data-stu-id="11185-167">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="11185-168">만 **하나의 HTTPS 포트** 앱에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-168">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="11185-169">미들웨어를 통해 포트 검색 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-169">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="11185-170">역방향 프록시 구성에서 앱 실행 될 때 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-170">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="11185-171">이 섹션에서 설명한 다른 방법 중 하나를 사용 하 여 포트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-171">Set the port using one of the other approaches described in this section.</span></span>

<span data-ttu-id="11185-172">Kestrel 또는 HTTP.sys를에 지 서버는 공용으로 사용 하면 둘 다에서 수신 대기 하도록 Kestrel 또는 HTTP.sys를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-172">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="11185-173">클라이언트가 리디렉션되는 위치 보안 포트 (일반적으로 프로덕션 및 개발에서 5001 443).</span><span class="sxs-lookup"><span data-stu-id="11185-173">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="11185-174">안전 하지 않은 포트 (일반적으로 프로덕션 환경에서 80) 및 개발에는 5000입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-174">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="11185-175">안전 하지 않은 포트는 안전 하지 않은 요청을 받고 보안 포트에 클라이언트를 리디렉션할 클라이언트 앱에 대 한 순서에서 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-175">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="11185-176">자세한 내용은 [Kestrel 끝점 구성을](xref:fundamentals/servers/kestrel#endpoint-configuration) 또는 <xref:fundamentals/servers/httpsys>합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-176">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="11185-177">배포 시나리오</span><span class="sxs-lookup"><span data-stu-id="11185-177">Deployment scenarios</span></span>

<span data-ttu-id="11185-178">클라이언트와 서버 간의 모든 방화벽 통신 포트가 트래픽에 대해 열려 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-178">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="11185-179">요청은 역방향 프록시 구성에서 전달 하는 경우 사용 하 여 [전달 된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer) HTTPS 리디렉션을 미들웨어를 호출 하기 전에 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-179">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="11185-180">헤더 미들웨어 업데이트를 전달 합니다 `Request.Scheme`를 사용 하 여는 `X-Forwarded-Proto` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-180">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="11185-181">미들웨어 허용 Uri 및 기타 보안 정책이 제대로 작동 하려면 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-181">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="11185-182">전달 된 헤더 미들웨어를 사용 하지 않는 경우 백 엔드 앱 올바른 스키마 수신 및 리디렉션 루프가 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-182">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="11185-183">일반적인 최종 사용자 오류 메시지 리디렉션이 너무 많습니다. 발생 한 경우</span><span class="sxs-lookup"><span data-stu-id="11185-183">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="11185-184">Azure App Service에 배포할 때의 지침에 따라 [자습서: 기존 사용자 지정 SSL 인증서를 Azure Web Apps에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11185-184">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="11185-185">옵션</span><span class="sxs-lookup"><span data-stu-id="11185-185">Options</span></span>

<span data-ttu-id="11185-186">다음 코드 호출을 강조 표시 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) 미들웨어 옵션을 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="11185-186">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="11185-187">호출 `AddHttpsRedirection` 값을 변경 하는 데 필요한 전용인 `HttpsPort` 또는 `RedirectStatusCode`합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-187">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="11185-188">위의 강조 표시 된 코드:</span><span class="sxs-lookup"><span data-stu-id="11185-188">The preceding highlighted code:</span></span>

* <span data-ttu-id="11185-189">집합 [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 에 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, 기본 값입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-189">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="11185-190">필드를 사용 합니다 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스에 대 한 할당 `RedirectStatusCode`합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-190">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="11185-191">HTTPS 포트를 5001로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-191">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="11185-192">기본값은 443입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-192">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="11185-193">프로덕션 환경에서 영구 리디렉션 구성</span><span class="sxs-lookup"><span data-stu-id="11185-193">Configure permanent redirects in production</span></span>

<span data-ttu-id="11185-194">미들웨어를 전송 하도록 기본값을 [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 모든 리디렉션을 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-194">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="11185-195">비 개발 환경에서 앱이 영구 리디렉션 상태 코드를 전송 하려는 경우 미들웨어 옵션 구성 된 비 개발 환경에 대 한 조건부 검사를 래핑하십시오.</span><span class="sxs-lookup"><span data-stu-id="11185-195">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="11185-196">구성 하는 경우는 `IWebHostBuilder` 에 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="11185-196">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="11185-197">HTTPS 리디렉션을 미들웨어에 대 한 대안 정보</span><span class="sxs-lookup"><span data-stu-id="11185-197">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="11185-198">HTTPS 리디렉션을 미들웨어를 사용 하는 대신 (`UseHttpsRedirection`) URL 재작성 미들웨어를 사용 하는 것 (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="11185-198">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="11185-199">`AddRedirectToHttps` 리디렉션 실행 될 때 상태 코드 및 포트를 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-199">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="11185-200">자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-200">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="11185-201">추가 리디렉션 규칙을 요구 하지 않고 HTTPS로 리디렉션, 하는 경우 HTTPS 리디렉션을 미들웨어를 사용 하는 것이 좋습니다 (`UseHttpsRedirection`)이이 항목에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-201">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="11185-202">합니다 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) HTTPS가 필요 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-202">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="11185-203">`[RequireHttpsAttribute]` 컨트롤러 또는 메서드를 데코레이팅 할 수 있습니다 또는 전체적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-203">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="11185-204">특성을 전역적으로 적용 하려면 다음 코드를 추가 합니다 `ConfigureServices` 에서 `Startup`:</span><span class="sxs-lookup"><span data-stu-id="11185-204">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="11185-205">위의 강조 표시된 코드는 모든 요청에 `HTTPS` 를 사용하도록 강제하며, 그 결과 HTTP 요청은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-205">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="11185-206">다음에 강조 표시된 코드는 모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-206">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="11185-207">자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="11185-208">또한 미들웨어 리디렉션 실행 될 때 상태 코드 또는 상태 코드와 포트를 설정 하려면 앱을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-208">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="11185-209">전역으로 HTTPS를 요구하는 것이 보안상 가장 안전한 모범 사례입니다 (`options.Filters.Add(new RequireHttpsAttribute());`).</span><span class="sxs-lookup"><span data-stu-id="11185-209">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="11185-210">적용 된 `[RequireHttps]` 모든 컨트롤러/Razor 페이지에는 특성에 전역적으로 HTTPS를 요구 하는 것 만큼 안전로 간주 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-210">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="11185-211">보장할 수 없습니다는 `[RequireHttps]` 새 컨트롤러 및 Razor 페이지는 추가 특성이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-211">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="11185-212">HTTP 엄격한 전송 보안 프로토콜 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="11185-212">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="11185-213">당 [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)하십시오 [HTTP 엄격한 전송 보안 (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) 응답 헤더를 사용 하 여 웹 앱에서 지정 하는 옵트인 보안 향상 된 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-213">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="11185-214">경우는 [HSTS를 지 원하는 브라우저](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) 이 헤더를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-214">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="11185-215">브라우저에는 모든 통신이 HTTP를 통해 보낼 수 없는 도메인에 대 한 구성을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-215">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="11185-216">브라우저 강제로 HTTPS를 통해 모든 통신을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-216">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="11185-217">브라우저에서 신뢰할 수 없거나 잘못 된 인증서를 사용 하 여 사용자를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-217">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="11185-218">브라우저 사용자가 일시적으로 이러한 인증서를 신뢰 하는 프롬프트를 비활성화 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-218">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="11185-219">HSTS는 클라이언트에서 적용 하기 때문에 몇 가지 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-219">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="11185-220">클라이언트는 HSTS를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-220">The client must support HSTS.</span></span>
* <span data-ttu-id="11185-221">HSTS는 HSTS 정책을 설정 하려면 하나 이상의 성공적인 HTTPS 요청에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-221">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="11185-222">응용 프로그램이 모든 HTTP 요청을 확인 하 고 리디렉션 하거나 HTTP 요청을 거부 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-222">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="11185-223">ASP.NET Core 2.1 이상을 사용 하 여 HSTS를 구현 합니다 `UseHsts` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="11185-223">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="11185-224">다음 코드 호출 `UseHsts` 에 앱이 없는 경우 [개발 모드](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="11185-224">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="11185-225">`UseHsts` 권장 되지 않습니다 개발에서 HSTS 설정이 항상 캐시할 수 있기 때문에 브라우저에서.</span><span class="sxs-lookup"><span data-stu-id="11185-225">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="11185-226">기본적으로 `UseHsts` 로컬 루프백 주소를 제외 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-226">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="11185-227">프로덕션 환경에 대 한 초기 설정 처음에 대 한 HTTPS를 구현 [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) 중 하나를 사용 하는 작은 값으로는 <xref:System.TimeSpan> 메서드.</span><span class="sxs-lookup"><span data-stu-id="11185-227">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="11185-228">값을 설정할 시간에서 전혀 보다 하루를 HTTP로 HTTPS 인프라를 되돌려야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="11185-228">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="11185-229">HTTPS 구성의 유지 가능성에 확신한 후 늘릴 HSTS 최대 처리 기간 값 자주 사용 되는 값은 1 년입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-229">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="11185-230">다음 예를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="11185-230">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="11185-231">Strict-전송-보안 헤더의 미리 로드 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-231">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="11185-232">Preload의 일부가 아닌 합니다 [RFC HSTS 사양](https://tools.ietf.org/html/rfc6797), 있지만 HSTS 새로 설치할 때 사이트를 미리 로드 하려면 웹 브라우저에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-232">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="11185-233">자세한 내용은 [https://hstspreload.org/](https://hstspreload.org/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11185-233">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="11185-234">사용 하도록 설정 [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), 호스트 하위 도메인에 HSTS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-234">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="11185-235">60 일로 Strict-전송-보안 헤더의 최대 처리 기간 매개 변수를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-235">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="11185-236">설정 하지 않으면 기본값은 30 일입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-236">If not set, defaults to 30 days.</span></span> <span data-ttu-id="11185-237">참조 된 [최대 처리 기간 지시문](https://tools.ietf.org/html/rfc6797#section-6.1.1) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-237">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="11185-238">추가 `example.com` 를 제외 하는 호스트의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-238">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="11185-239">`UseHsts` 다음 루프백 호스트를 제외:</span><span class="sxs-lookup"><span data-stu-id="11185-239">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="11185-240">`localhost`은: IPv4 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-240">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="11185-241">`127.0.0.1`은: IPv4 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-241">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="11185-242">`[::1]`은: IPv6 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-242">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="11185-243">옵트아웃 HTTPS/HSTS 프로젝트 생성 시</span><span class="sxs-lookup"><span data-stu-id="11185-243">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="11185-244">연결 보안 공용 네트워크의에 지에서 처리 되는 일부 백 엔드 서비스 시나리오에서는 각 노드에 연결 보안 구성 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="11185-244">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="11185-245">웹 앱 또는 Visual Studio의 템플릿에서 생성 된 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령 사용 [HTTPS 간의 리디렉션으로](#require-https) 및 [HSTS](#http-strict-transport-security-protocol-hsts)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-245">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="11185-246">이러한 시나리오를 필요로 하지 않는 배포에 대 한 있습니다 수 옵트아웃 HTTPS/HSTS 서식 파일에서 앱을 만들 때.</span><span class="sxs-lookup"><span data-stu-id="11185-246">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="11185-247">옵트아웃 하려면 HTTPS/HSTS입니다.</span><span class="sxs-lookup"><span data-stu-id="11185-247">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11185-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11185-248">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="11185-249">선택 취소 합니다 **HTTPS에 대 한 구성** 확인란 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-249">Uncheck the **Configure for HTTPS** check box.</span></span>

![새 ASP.NET Core 웹 응용 프로그램 대화 상자 표시 HTTPS 확인란의 선택을 취소에 대 한 구성입니다.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="11185-251">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="11185-251">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="11185-252">`--no-https` 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-252">Use the `--no-https` option.</span></span> <span data-ttu-id="11185-253">예</span><span class="sxs-lookup"><span data-stu-id="11185-253">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="11185-254">Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서를 신뢰 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-254">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="11185-255">.NET core SDK에는 HTTPS 개발 인증서를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-255">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="11185-256">인증서는 첫 실행 경험의 일부로 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-256">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="11185-257">예를 들어 `dotnet --info` 다음과 유사한 출력을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-257">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="11185-258">.NET Core SDK를 설치하면 로컬 사용자 인증서 저장소에 ASP.NET Core HTTPS 개발 인증서가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="11185-258">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="11185-259">인증서가 설치 되었지만 신뢰할 수 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-259">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="11185-260">인증서 신뢰 dotnet 실행 하는 일회성 단계를 수행 `dev-certs` 도구:</span><span class="sxs-lookup"><span data-stu-id="11185-260">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="11185-261">다음 명령은 `dev-certs` 도구에 대한 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-261">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="11185-262">Docker에 대 한 개발자 인증서를 설정 하는 방법</span><span class="sxs-lookup"><span data-stu-id="11185-262">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="11185-263">참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6199)합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-263">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="11185-264">Linux 용 Windows 하위 시스템에서 HTTPS 인증서를 신뢰 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-264">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="11185-265">Windows 하위 시스템에 대 한 WSL (Linux) HTTPS 자체 서명 된 인증서를 생성합니다. WSL 인증서를 신뢰 하는 Windows 인증서 저장소를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-265">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="11185-266">WSL 생성 된 인증서를 내보내려면 다음 명령을 실행 합니다. `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="11185-266">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="11185-267">WSL 창에서 다음 명령을 실행 합니다. `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="11185-267">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="11185-268">위의 명령은 Linux Windows 신뢰할 수 있는 인증서를 사용 하므로 환경 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="11185-268">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="11185-269">추가 정보</span><span class="sxs-lookup"><span data-stu-id="11185-269">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="11185-270">Apache 사용 하 여 Linux에서 ASP.NET Core를 호스트 합니다. HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="11185-270">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="11185-271">Nginx 사용 하 여 Linux에서 ASP.NET Core를 호스트 합니다. HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="11185-271">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="11185-272">IIS에서 SSL 설정 하는 방법</span><span class="sxs-lookup"><span data-stu-id="11185-272">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="11185-273">OWASP HSTS 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="11185-273">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
