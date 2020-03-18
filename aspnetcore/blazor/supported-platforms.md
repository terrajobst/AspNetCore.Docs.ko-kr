---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647109"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="0ce1d-103">ASP.NET Core Blazor 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="0ce1d-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="0ce1d-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="0ce1d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="0ce1d-105">브라우저 요구 사항</span><span class="sxs-lookup"><span data-stu-id="0ce1d-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="0ce1d-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ce1d-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="0ce1d-107">브라우저</span><span class="sxs-lookup"><span data-stu-id="0ce1d-107">Browser</span></span>                          | <span data-ttu-id="0ce1d-108">버전</span><span class="sxs-lookup"><span data-stu-id="0ce1d-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="0ce1d-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0ce1d-109">Microsoft Edge</span></span>                   | <span data-ttu-id="0ce1d-110">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-110">Current</span></span>               |
| <span data-ttu-id="0ce1d-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0ce1d-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="0ce1d-112">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-112">Current</span></span>               |
| <span data-ttu-id="0ce1d-113">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="0ce1d-113">Google Chrome, including Android</span></span> | <span data-ttu-id="0ce1d-114">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-114">Current</span></span>               |
| <span data-ttu-id="0ce1d-115">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="0ce1d-115">Safari, including iOS</span></span>            | <span data-ttu-id="0ce1d-116">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-116">Current</span></span>               |
| <span data-ttu-id="0ce1d-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0ce1d-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="0ce1d-118">지원되지 않음&dagger;</span><span class="sxs-lookup"><span data-stu-id="0ce1d-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="0ce1d-119">&dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0ce1d-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="0ce1d-120">Blazor 서버</span><span class="sxs-lookup"><span data-stu-id="0ce1d-120">Blazor Server</span></span>

| <span data-ttu-id="0ce1d-121">브라우저</span><span class="sxs-lookup"><span data-stu-id="0ce1d-121">Browser</span></span>                          | <span data-ttu-id="0ce1d-122">버전</span><span class="sxs-lookup"><span data-stu-id="0ce1d-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="0ce1d-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0ce1d-123">Microsoft Edge</span></span>                   | <span data-ttu-id="0ce1d-124">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-124">Current</span></span>    |
| <span data-ttu-id="0ce1d-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0ce1d-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="0ce1d-126">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-126">Current</span></span>    |
| <span data-ttu-id="0ce1d-127">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="0ce1d-127">Google Chrome, including Android</span></span> | <span data-ttu-id="0ce1d-128">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-128">Current</span></span>    |
| <span data-ttu-id="0ce1d-129">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="0ce1d-129">Safari, including iOS</span></span>            | <span data-ttu-id="0ce1d-130">현재</span><span class="sxs-lookup"><span data-stu-id="0ce1d-130">Current</span></span>    |
| <span data-ttu-id="0ce1d-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0ce1d-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="0ce1d-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="0ce1d-132">11&dagger;</span></span> |

<span data-ttu-id="0ce1d-133">&dagger;추가 보충 기능이 필요합니다(예: [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 프라미스를 추가할 수 있음).</span><span class="sxs-lookup"><span data-stu-id="0ce1d-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ce1d-134">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0ce1d-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
