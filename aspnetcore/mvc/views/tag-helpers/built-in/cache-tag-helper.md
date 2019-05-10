---
title: ASP.NET Core MVC의 캐시 태그 도우미
author: pkellner
description: 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: fb69584f6e9d4756e175bbd6f3deb1f413b80fc5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890468"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="b7f3e-103">ASP.NET Core MVC의 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="b7f3e-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="b7f3e-104">작성자: [Peter Kellner](http://peterkellner.net) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b7f3e-104">By [Peter Kellner](http://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span> 

<span data-ttu-id="b7f3e-105">캐시 태그 도우미는 콘텐츠를 내부 ASP.NET Core 캐시 공급자에 캐시하여 ASP.NET Core 앱 성능을 개선하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="b7f3e-106">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="b7f3e-107">다음 Razor 태그는 현재 날짜를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="b7f3e-108">태그 도우미를 포함하는 페이지에 대한 첫 번째 요청은 현재 날짜를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="b7f3e-109">추가 요청은 캐시가 만료될 때까지(기본값 20분) 또는 캐시에서 캐시된 날짜가 제거될 때까지 캐시된 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="b7f3e-110">캐시 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="b7f3e-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="b7f3e-111">사용</span><span class="sxs-lookup"><span data-stu-id="b7f3e-111">enabled</span></span>

| <span data-ttu-id="b7f3e-112">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-112">Attribute Type</span></span>  | <span data-ttu-id="b7f3e-113">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-113">Examples</span></span>        | <span data-ttu-id="b7f3e-114">기본값</span><span class="sxs-lookup"><span data-stu-id="b7f3e-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="b7f3e-115">부울</span><span class="sxs-lookup"><span data-stu-id="b7f3e-115">Boolean</span></span>         | <span data-ttu-id="b7f3e-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="b7f3e-117">`enabled`는 캐시 태그 도우미로 묶인 콘텐츠가 캐시되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="b7f3e-118">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-118">The default is `true`.</span></span> <span data-ttu-id="b7f3e-119">`false`로 설정하면 렌더링된 출력이 캐시되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="b7f3e-120">예제:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="b7f3e-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="b7f3e-121">expires-on</span></span>

| <span data-ttu-id="b7f3e-122">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-122">Attribute Type</span></span>   | <span data-ttu-id="b7f3e-123">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="b7f3e-124">`expires-on`은 캐시된 항목의 절대 만료 날짜를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="b7f3e-125">다음 예제는 2025년 1월 29일 오후 5시 2분이 될 때까지 캐시 태그 도우미의 콘텐츠를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="b7f3e-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="b7f3e-126">expires-after</span></span>

| <span data-ttu-id="b7f3e-127">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-127">Attribute Type</span></span> | <span data-ttu-id="b7f3e-128">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-128">Example</span></span>                      | <span data-ttu-id="b7f3e-129">기본값</span><span class="sxs-lookup"><span data-stu-id="b7f3e-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="b7f3e-130">20분</span><span class="sxs-lookup"><span data-stu-id="b7f3e-130">20 minutes</span></span> |

<span data-ttu-id="b7f3e-131">`expires-after`는 콘텐츠를 캐시할 첫 번째 요청 시간부터 시간 길이를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="b7f3e-132">예제:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b7f3e-133">Razor 보기 엔진은 기본 `expires-after` 값을 20분으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="b7f3e-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="b7f3e-134">expires-sliding</span></span>

| <span data-ttu-id="b7f3e-135">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-135">Attribute Type</span></span> | <span data-ttu-id="b7f3e-136">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="b7f3e-137">캐시 항목 값이 액세스되지 않는 경우 캐시 항목을 제거해야 하는 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="b7f3e-138">예제:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="b7f3e-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="b7f3e-139">vary-by-header</span></span>

| <span data-ttu-id="b7f3e-140">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-140">Attribute Type</span></span> | <span data-ttu-id="b7f3e-141">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="b7f3e-142">문자열</span><span class="sxs-lookup"><span data-stu-id="b7f3e-142">String</span></span>         | <span data-ttu-id="b7f3e-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="b7f3e-144">`vary-by-header`는 값이 변경되면 캐시 새로 고침을 트리거하는 쉼표로 구분된 헤더 값 목록을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="b7f3e-145">다음 예제에서는 헤더 값 `User-Agent`를 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="b7f3e-146">이 예제는 웹 서버에 제공된 모든 `User-Agent`에 대한 콘텐츠를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="b7f3e-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="b7f3e-147">vary-by-query</span></span>

| <span data-ttu-id="b7f3e-148">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-148">Attribute Type</span></span> | <span data-ttu-id="b7f3e-149">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="b7f3e-150">문자열</span><span class="sxs-lookup"><span data-stu-id="b7f3e-150">String</span></span>         | <span data-ttu-id="b7f3e-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="b7f3e-152">`vary-by-query`는 나열된 키 값이 변경될 때 캐시 새로 고침을 트리거하는 쿼리 문자열(<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>)에 쉼표로 구분된 <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> 목록을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="b7f3e-153">다음 예제에서는 `Make` 및 `Model` 값을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="b7f3e-154">이 예제는 웹 서버에 제공된 모든 `Make` 및 `Model`에 대한 콘텐츠를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="b7f3e-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="b7f3e-155">vary-by-route</span></span>

| <span data-ttu-id="b7f3e-156">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-156">Attribute Type</span></span> | <span data-ttu-id="b7f3e-157">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="b7f3e-158">문자열</span><span class="sxs-lookup"><span data-stu-id="b7f3e-158">String</span></span>         | <span data-ttu-id="b7f3e-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="b7f3e-160">`vary-by-route`는 경로 데이터 매개 변수 값이 변경되면 캐시 새로 고침을 트리거하는 쉼표로 구분된 경로 매개 변수 이름 목록을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="b7f3e-161">예제:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-161">Example:</span></span>

<span data-ttu-id="b7f3e-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="b7f3e-163">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="b7f3e-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="b7f3e-164">vary-by-cookie</span></span>

| <span data-ttu-id="b7f3e-165">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-165">Attribute Type</span></span> | <span data-ttu-id="b7f3e-166">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="b7f3e-167">문자열</span><span class="sxs-lookup"><span data-stu-id="b7f3e-167">String</span></span>         | <span data-ttu-id="b7f3e-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="b7f3e-169">`vary-by-cookie`는 쿠키 값이 변경되면 캐시 새로 고침을 트리거하는 쉼표로 구분된 쿠키 이름 목록을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="b7f3e-170">다음 예제에서는 ASP.NET Core ID와 연결된 쿠키를 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="b7f3e-171">사용자가 인증되면 ID 쿠키가 변경될 때 캐시 새로 고침이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="b7f3e-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="b7f3e-172">vary-by-user</span></span>

| <span data-ttu-id="b7f3e-173">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-173">Attribute Type</span></span>  | <span data-ttu-id="b7f3e-174">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-174">Examples</span></span>        | <span data-ttu-id="b7f3e-175">기본값</span><span class="sxs-lookup"><span data-stu-id="b7f3e-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="b7f3e-176">부울</span><span class="sxs-lookup"><span data-stu-id="b7f3e-176">Boolean</span></span>         | <span data-ttu-id="b7f3e-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="b7f3e-178">`vary-by-user`는 로그인한 사용자(또는 컨텍스트 보안 주체)가 변경되면 캐시를 다시 설정해야 하는지 여부를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="b7f3e-179">현재 사용자를 요청 컨텍스트 보안 주체라고도 하며 `@User.Identity.Name`을 참조하여 Razor 보기에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="b7f3e-180">다음 예에서는 현재 로그인한 사용자를 모니터링하여 캐시 새로 고침을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b7f3e-181">이 특성을 사용하면 로그인 및 로그아웃 주기 동안 콘텐츠가 캐시에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="b7f3e-182">값이 `true`로 설정되면 인증 주기로 인해 인증된 사용자의 캐시가 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="b7f3e-183">사용자가 인증되면 새로운 고유 쿠키 값이 생성되므로 캐시가 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="b7f3e-184">쿠키가 없거나 만료된 경우 캐시는 익명 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="b7f3e-185">사용자가 인증되지 **않으면** 캐시가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="b7f3e-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="b7f3e-186">vary-by</span></span>

| <span data-ttu-id="b7f3e-187">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-187">Attribute Type</span></span> | <span data-ttu-id="b7f3e-188">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="b7f3e-189">문자열</span><span class="sxs-lookup"><span data-stu-id="b7f3e-189">String</span></span>         | `@Model` |

<span data-ttu-id="b7f3e-190">`vary-by`는 캐시되는 데이터의 사용자 지정을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="b7f3e-191">특성의 문자열 값에서 참조하는 개체가 변경되면 캐시 태그 도우미의 콘텐츠가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="b7f3e-192">종종 모델 값의 문자열 연결이 이 특성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="b7f3e-193">실제로 연결된 값을 업데이트하면 캐시가 무효화되는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="b7f3e-194">다음 예제에서는 보기를 렌더링하는 컨트롤러 메서드가 두 경로 매개 변수 `myParam1` 및 `myParam2`의 정수 값을 합산하고, 합계를 단일 모델 속성으로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="b7f3e-195">이 합계가 변경되면 캐시 태그 도우미의 콘텐츠가 다시 렌더링 및 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="b7f3e-196">작업:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="b7f3e-197">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="b7f3e-198">priority</span><span class="sxs-lookup"><span data-stu-id="b7f3e-198">priority</span></span>

| <span data-ttu-id="b7f3e-199">특성 유형</span><span class="sxs-lookup"><span data-stu-id="b7f3e-199">Attribute Type</span></span>      | <span data-ttu-id="b7f3e-200">예제</span><span class="sxs-lookup"><span data-stu-id="b7f3e-200">Examples</span></span>                               | <span data-ttu-id="b7f3e-201">기본값</span><span class="sxs-lookup"><span data-stu-id="b7f3e-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="b7f3e-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="b7f3e-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="b7f3e-203">`priority`는 기본 제공 캐시 공급자에게 캐시 제거 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="b7f3e-204">웹 서버는 메모리가 부족할 경우 가장 먼저 `Low` 캐시 항목을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="b7f3e-205">예제:</span><span class="sxs-lookup"><span data-stu-id="b7f3e-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b7f3e-206">`priority` 특성은 특정 수준의 캐시 보존을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="b7f3e-207">`CacheItemPriority`는 권장 사항일 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="b7f3e-208">이 특성을 `NeverRemove`로 설정해도 캐시된 항목이 항상 보존된다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="b7f3e-209">자세한 내용은 [추가 리소스](#additional-resources) 섹션의 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="b7f3e-210">캐시 태그 도우미는 [메모리 캐시 서비스](xref:performance/caching/memory)에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="b7f3e-211">서비스가 추가되지 않은 경우 캐시 태그 도우미가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f3e-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7f3e-212">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b7f3e-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
