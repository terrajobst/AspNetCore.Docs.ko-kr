---
title: ASP.NET Core에서 정책 구성표
author: rick-anderson
description: 인증 정책 체계 쉽게 단일 논리 인증 체계
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: c310b61e14df2b7846e32a602bb75914a5850aff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895200"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="23487-103">ASP.NET Core에서 정책 구성표</span><span class="sxs-lookup"><span data-stu-id="23487-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="23487-104">인증 정책 스키마 쉽게 잠재적으로 여러 가지 방법을 사용 하 여 단일 논리 인증 체계를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23487-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="23487-105">예를 들어 정책 구성표를 제외한 다른 과제에 대해 Google 인증 및 쿠키 인증 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23487-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="23487-106">인증 정책 체계 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="23487-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="23487-107">모든 인증 작업을 다른 체계를 전달 하는 일을 쉽게 합니다.</span><span class="sxs-lookup"><span data-stu-id="23487-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="23487-108">요청에 따라 동적으로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="23487-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="23487-109">사용 하 여 파생 된 모든 인증 체계 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 및 관련 [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="23487-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="23487-110">ASP.NET Core 2.1 이상 정책 스키마를 자동으로 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23487-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="23487-111">구성 체계의 옵션을 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23487-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="23487-112">예제</span><span class="sxs-lookup"><span data-stu-id="23487-112">Examples</span></span>

<span data-ttu-id="23487-113">다음 예제에서는 하위 수준 체계를 결합 하는 더 높은 수준 체계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="23487-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="23487-114">문제를 Google 인증을 사용 하 고 다른 모든 항목에 대 한 쿠키 인증을 사용 하는 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="23487-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="23487-115">다음 예제에서는 요청 별로 스키마를 동적으로 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23487-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="23487-116">즉, 쿠키 및 API 인증을 조합 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="23487-116">That is, how to mix cookies and API authentication.</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
