---
title: ASP.NET Core에서 오픈 리디렉션 공격 방지
author: ardalis
description: ASP.NET Core 앱에 대 한 오픈 리디렉션 공격을 방지 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 0896189d2caaccb19647eb7c6d57f29dfc0290dd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891620"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="3d7ed-103">ASP.NET Core에서 오픈 리디렉션 공격 방지</span><span class="sxs-lookup"><span data-stu-id="3d7ed-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="3d7ed-104">잠재적으로 쿼리 문자열 또는 폼 데이터와 같은 요청을 통해 지정 된 URL로 리디렉션하는 웹 앱을 외부, 악성 URL에 사용자를 리디렉션할 사용 하 여 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="3d7ed-105">이 변조 오픈 리디렉션 공격을 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="3d7ed-106">때마다 응용 프로그램 논리를 지정 된 URL로 리디렉션되면 리디렉션 URL을 사용 하 여 손상 되지 않았는지를 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="3d7ed-107">ASP.NET Core 앱 (라고도 오픈 리디렉션) 오픈 리디렉션 공격 으로부터 보호 하기 위해 기본 제공 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="3d7ed-108">오픈 리디렉션 공격 란?</span><span class="sxs-lookup"><span data-stu-id="3d7ed-108">What is an open redirect attack?</span></span>

<span data-ttu-id="3d7ed-109">웹 응용 프로그램 자주 사용자를 리디렉션할 로그인 페이지를 인증 해야 하는 리소스에 액세스할 때.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="3d7ed-110">리디렉션 일반적으로 포함 됩니다는 `returnUrl` querystring 매개 변수를 성공적으로 로그인 한 후 원래 요청 된 URL에 반환 될 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="3d7ed-111">사용자 인증 후가 원래 요청 된 URL로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="3d7ed-112">대상 URL을 요청의 쿼리 문자열에 지정 되어 있으므로 악의적인 사용자는 querystring 변조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="3d7ed-113">변조 된 querystring 사이트 외부의 악의적인 사이트에 사용자를 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="3d7ed-114">이 기술은 오픈 리디렉션 (또는 리디렉션) 공격을 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="3d7ed-115">예제에서는 공격</span><span class="sxs-lookup"><span data-stu-id="3d7ed-115">An example attack</span></span>

<span data-ttu-id="3d7ed-116">악의적인 사용자는 사용자의 자격 증명이 나 중요 한 정보를 악의적인 사용자가 액세스를 허용 하는 데 공격을 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="3d7ed-117">악의적인 사용자의 공격을 시작 하려면 사용자가 사용 하 여 사이트의 로그인 페이지에 대 한 링크를 클릭을 유도 한 `returnUrl` querystring 값을 URL에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="3d7ed-118">예를 들어 앱에서 고려해 야 `contoso.com` 에서 로그인 페이지를 포함 하는 `http://contoso.com/Account/LogOn?returnUrl=/Home/About`합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="3d7ed-119">공격은 이러한 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="3d7ed-120">사용자는 악성 링크를 클릭 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (두 번째 URL은 "contoso**1**.com"에 없습니다 "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="3d7ed-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="3d7ed-121">사용자가 성공적으로 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="3d7ed-122">사용자가 리디렉션됩니다 (사이트)에서 `http://contoso1.com/Account/LogOn` (실제 사이트 처럼 정확 하 게 보이는 악의적인 사이트).</span><span class="sxs-lookup"><span data-stu-id="3d7ed-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="3d7ed-123">사용자가 다시 로그인 (자격 증명을 사이트 악의적인 제공) 및 실제 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="3d7ed-124">가능성이 사용자 로그인에 해당 첫 번째 시도가 실패 했음을 두 번째 시도가 성공적으로 되었는지 생각 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="3d7ed-125">사용자는 대부분 해당 자격 증명이 손상 된를 인식 하지 못하는 남아 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![오픈 리디렉션 공격 프로세스](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="3d7ed-127">로그인 페이지 외에도 일부 사이트 리디렉션 페이지 또는 끝점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="3d7ed-128">앱에는 오픈 리디렉션으로 페이지가 imagine `/Home/Redirect`합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="3d7ed-129">침입자가 만들, 예를 들어으로 이동 하는 전자 메일의 링크 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="3d7ed-130">일반적인 사용자는 URL을 살펴보고 사이트 이름으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="3d7ed-131">신뢰, 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="3d7ed-132">오픈 리디렉션은 다음 사용자를 즐기세요 동일한 보이는, 피싱 사이트로 보내고 사용자 했을 것 로그인 있다고 생각 하는 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="3d7ed-133">오픈 리디렉션 공격 으로부터 보호</span><span class="sxs-lookup"><span data-stu-id="3d7ed-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="3d7ed-134">웹 응용 프로그램을 개발할 때으로 신뢰할 수 없는 모든 사용자가 제공한 데이터를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="3d7ed-135">응용 프로그램 URL의 내용을 기반으로 사용자를 리디렉션하는 기능에는 이러한 리디렉션만 이루어집니다 로컬로 앱 내에서 (또는 알려진된 URL 쿼리 문자열에서 지정할 수는 없습니다 URL)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="3d7ed-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="3d7ed-136">LocalRedirect</span></span>

<span data-ttu-id="3d7ed-137">사용 된 `LocalRedirect` 도우미 메서드에서 기본 `Controller` 클래스:</span><span class="sxs-lookup"><span data-stu-id="3d7ed-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="3d7ed-138">`LocalRedirect` 로컬이 아닌 URL을 지정 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="3d7ed-139">그렇지 않은 경우 처럼 작동 합니다 `Redirect` 메서드.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="3d7ed-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="3d7ed-140">IsLocalUrl</span></span>

<span data-ttu-id="3d7ed-141">사용 된 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 리디렉션하기 전에 Url을 테스트 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="3d7ed-142">다음 예제에서는 URL을 리디렉션하기 전에 로컬 인지 여부를 확인 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="3d7ed-143">`IsLocalUrl` 메서드 악의적인 사이트에 리디렉션되지 실수로 사용자를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="3d7ed-144">로컬이 아닌 URL은 로컬 URL을 필요한 상황에서 제공 되는 경우 제공 된 URL의 세부 정보를 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="3d7ed-145">로깅 리디렉션 Url 리디렉션 공격 진단에 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d7ed-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
