---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: efa177245faceddad4eb80de9e6f6d38e1a4261c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022420"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="debe5-103">ASP.NET Core에 대 한 인증 샘플</span><span class="sxs-lookup"><span data-stu-id="debe5-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="debe5-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="debe5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="debe5-105">[ASP.NET Core 리포지토리에](https://github.com/aspnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="debe5-106">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="debe5-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="debe5-107">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="debe5-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="debe5-108">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="debe5-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="debe5-109">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="debe5-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="debe5-110">외부 클레임</span><span class="sxs-lookup"><span data-stu-id="debe5-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="debe5-111">요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="debe5-112">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="debe5-113">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="debe5-113">Run the samples</span></span>

* <span data-ttu-id="debe5-114">[분기](https://github.com/aspnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="debe5-115">예를 들면 `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="debe5-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="debe5-116">[ASP.NET Core 리포지토리](https://github.com/aspnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="debe5-117">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://www.microsoft.com/net/download/all) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="debe5-118">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="debe5-119">[ASP.NET Core 리포지토리에](https://github.com/aspnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="debe5-120">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="debe5-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="debe5-121">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="debe5-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="debe5-122">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="debe5-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="debe5-123">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="debe5-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="debe5-124">외부 클레임</span><span class="sxs-lookup"><span data-stu-id="debe5-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="debe5-125">요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="debe5-126">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="debe5-127">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="debe5-127">Run the samples</span></span>

* <span data-ttu-id="debe5-128">[분기](https://github.com/aspnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="debe5-129">예를 들면 `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="debe5-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="debe5-130">[ASP.NET Core 리포지토리](https://github.com/aspnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="debe5-131">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://www.microsoft.com/net/download/all) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="debe5-132">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="debe5-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
