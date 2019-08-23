---
title: ASP.NET Core에 대 한 클라이언트 IP safelist
author: damienbod
description: 승인 된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사 하는 미들웨어 또는 작업 필터를 작성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: 02e44135ab1742d44691cfda8c4167f21d6efa4e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975640"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="10cf0-103">ASP.NET Core에 대 한 클라이언트 IP safelist</span><span class="sxs-lookup"><span data-stu-id="10cf0-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="10cf0-104">[Damien Bowden](https://twitter.com/damien_bod) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="10cf0-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="10cf0-105">이 문서에서는 ASP.NET Core 앱에서 IP safelist (허용 목록 라고도 함)를 구현 하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="10cf0-106">다음을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-106">You can use:</span></span>

* <span data-ttu-id="10cf0-107">미들웨어를 통해 모든 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="10cf0-108">작업 필터는 특정 컨트롤러 또는 작업 메서드에 대 한 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="10cf0-109">Razor Pages 필터는 Razor 페이지에 대 한 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="10cf0-110">각각의 경우 승인 된 클라이언트 IP 주소를 포함 하는 문자열은 앱 설정에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="10cf0-111">미들웨어 또는 필터는 문자열을 목록으로 구문 분석 하 고, 원격 IP가 목록에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="10cf0-112">그렇지 않으면 HTTP 403 사용할 수 없음 상태 코드가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="10cf0-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10cf0-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="10cf0-114">Safelist</span><span class="sxs-lookup"><span data-stu-id="10cf0-114">The safelist</span></span>

<span data-ttu-id="10cf0-115">이 목록은 *appsettings* 파일에 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="10cf0-116">세미콜론으로 구분 된 목록이 며 IPv4 및 IPv6 주소를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="10cf0-117">미들웨어</span><span class="sxs-lookup"><span data-stu-id="10cf0-117">Middleware</span></span>

<span data-ttu-id="10cf0-118">메서드 `Configure` 는 미들웨어를 추가 하 고 생성자 매개 변수에 safelist 문자열을 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="10cf0-119">미들웨어는 문자열을 배열로 구문 분석 하 고 배열에서 원격 IP 주소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="10cf0-120">원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 401을 사용할 수 없음으로 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="10cf0-121">이 유효성 검사 프로세스는 HTTP Get 요청에 대해 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="10cf0-122">작업 필터</span><span class="sxs-lookup"><span data-stu-id="10cf0-122">Action filter</span></span>

<span data-ttu-id="10cf0-123">특정 컨트롤러 또는 작업 메서드에만 safelist을 사용 하려면 작업 필터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="10cf0-124">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="10cf0-125">작업 필터가 서비스 컨테이너에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="10cf0-126">그러면 컨트롤러 또는 작업 메서드에서 필터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="10cf0-127">샘플 앱에서 필터는 `Get` 메서드에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="10cf0-128">따라서 `Get` API 요청을 전송 하 여 앱을 테스트 하는 경우 특성은 클라이언트 IP 주소의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="10cf0-129">다른 HTTP 메서드를 사용 하 여 API를 호출 하 여 테스트 하는 경우 미들웨어는 클라이언트 IP의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="10cf0-130">Razor Pages 필터</span><span class="sxs-lookup"><span data-stu-id="10cf0-130">Razor Pages filter</span></span> 

<span data-ttu-id="10cf0-131">Razor Pages 앱에 대 한 safelist을 사용 하려면 Razor Pages 필터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="10cf0-132">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="10cf0-133">이 필터는 MVC Filters 컬렉션에 추가 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="10cf0-134">앱을 실행 하 고 Razor 페이지를 요청 하면 Razor Pages 필터에서 클라이언트 IP의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="10cf0-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="10cf0-135">다음 단계</span><span class="sxs-lookup"><span data-stu-id="10cf0-135">Next steps</span></span>

<span data-ttu-id="10cf0-136">[ASP.NET Core 미들웨어에 대해 자세히 알아보세요](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="10cf0-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
