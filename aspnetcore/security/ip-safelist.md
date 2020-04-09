---
title: ASP.NET 코어에 대한 클라이언트 IP 보호 목록
author: damienbod
description: 미들웨어 또는 작업 필터를 작성하여 승인된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471806"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="889e6-103">ASP.NET 코어에 대한 클라이언트 IP 보호 목록</span><span class="sxs-lookup"><span data-stu-id="889e6-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="889e6-104">[데미안 보든과](https://twitter.com/damien_bod) [톰 다이크스트라](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="889e6-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="889e6-105">이 문서에서는 ASP.NET 핵심 앱에서 IP 주소 허용 목록(허용 목록이라고도 함)을 구현하는 세 가지 방법을 보여 주십니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="889e6-106">함께 제공되는 샘플 앱은 세 가지 방법을 모두 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="889e6-107">다음을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-107">You can use:</span></span>

* <span data-ttu-id="889e6-108">미들웨어는 모든 요청의 원격 IP 주소를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="889e6-109">MVC 작업 필터는 특정 컨트롤러 또는 작업 메서드에 대한 요청의 원격 IP 주소를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="889e6-110">Razor 페이지 필터를 사용하여 Razor 페이지에 대한 요청의 원격 IP 주소를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="889e6-111">각각의 경우 승인된 클라이언트 IP 주소가 포함된 문자열이 앱 설정에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="889e6-112">미들웨어 또는 필터:</span><span class="sxs-lookup"><span data-stu-id="889e6-112">The middleware or filter:</span></span>

* <span data-ttu-id="889e6-113">문자열을 배열로 구문 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="889e6-114">원격 IP 주소가 어레이에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="889e6-115">배열에 IP 주소가 포함된 경우 액세스가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="889e6-116">그렇지 않으면 HTTP 403 금지 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="889e6-117">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="889e6-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="889e6-118">IP 주소 보호 목록</span><span class="sxs-lookup"><span data-stu-id="889e6-118">IP address safelist</span></span>

<span data-ttu-id="889e6-119">샘플 앱에서 IP 주소 보호 목록은 다음과 같은</span><span class="sxs-lookup"><span data-stu-id="889e6-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="889e6-120">`AdminSafeList` *appsettings.json* 파일의 속성에 의해 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="889e6-121">[인터넷 프로토콜 버전 4(IPv4)](https://wikipedia.org/wiki/IPv4) 및 인터넷 프로토콜 버전 [6(IPv6)](https://wikipedia.org/wiki/IPv6) 주소를 모두 포함할 수 있는 세미콜론 구분 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="889e6-122">앞의 예제에서는 IPv4 주소 `127.0.0.1` 및 `192.168.1.5` IPv6 루프백 `::1` 주소(압축 형식용)가 `0:0:0:0:0:0:0:1`허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="889e6-123">미들웨어</span><span class="sxs-lookup"><span data-stu-id="889e6-123">Middleware</span></span>

<span data-ttu-id="889e6-124">메서드는 `Startup.Configure` 앱의 `AdminSafeListMiddleware` 요청 파이프라인에 사용자 지정 미들웨어 형식을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="889e6-125">사용 가능한 목록은 .NET Core 구성 공급자와 함께 검색되고 생성자 매개 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="889e6-126">미들웨어는 문자열을 배열로 구문 분석하고 배열의 원격 IP 주소를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="889e6-127">원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 403 금지됨을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="889e6-128">이 유효성 검사 프로세스는 HTTP GET 요청에 대해 우회됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="889e6-129">작업 필터</span><span class="sxs-lookup"><span data-stu-id="889e6-129">Action filter</span></span>

<span data-ttu-id="889e6-130">특정 MVC 컨트롤러 또는 작업 메서드에 대한 safelist 기반 액세스 제어를 원하는 경우 작업 필터를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="889e6-131">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="889e6-132">에서 `Startup.ConfigureServices`작업 필터를 MVC 필터 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="889e6-133">다음 예제에서는 작업 `ClientIpCheckActionFilter` 필터가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="889e6-134">보호 목록 및 콘솔 로거 인스턴스는 생성자 매개 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="889e6-135">그런 다음 [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) 특성을 사용하여 작업 필터를 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="889e6-136">샘플 앱에서 작업 필터는 컨트롤러의 `Get` 작업 메서드에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="889e6-137">다음을 전송하여 앱을 테스트할 때:</span><span class="sxs-lookup"><span data-stu-id="889e6-137">When you test the app by sending:</span></span>

* <span data-ttu-id="889e6-138">HTTP GET 요청, `[ServiceFilter]` 특성은 클라이언트 IP 주소의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="889e6-139">작업 메서드에 대한 `Get` 액세스가 허용되면 작업 필터 및 작업 메서드에 의해 다음 콘솔 출력의 변형이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="889e6-140">`AdminSafeListMiddleware` GET 이외의 HTTP 요청 동사는 미들웨어가 클라이언트 IP 주소의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="889e6-141">면도기 페이지 필터</span><span class="sxs-lookup"><span data-stu-id="889e6-141">Razor Pages filter</span></span>

<span data-ttu-id="889e6-142">Razor 페이지 앱에 대한 세이프리스트 기반 액세스 제어를 하려면 Razor 페이지 필터를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="889e6-143">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="889e6-144">에서 `Startup.ConfigureServices`MVC 필터 컬렉션에 추가하여 Razor 페이지 필터를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="889e6-145">다음 예제에서는 Razor `ClientIpCheckPageFilter` 페이지 필터가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="889e6-146">보호 목록 및 콘솔 로거 인스턴스는 생성자 매개 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="889e6-147">샘플 앱의 *색인* 면도기 페이지가 요청되면 Razor 페이지 필터는 클라이언트 IP 주소의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="889e6-148">필터는 다음과 같은 콘솔 출력의 변형을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="889e6-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="889e6-149">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="889e6-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="889e6-150">작업 필터</span><span class="sxs-lookup"><span data-stu-id="889e6-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
