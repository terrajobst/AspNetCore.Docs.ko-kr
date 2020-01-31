---
title: ASP.NET Core 웹 SDK
author: Rick-Anderson
description: Microsoft .NET Sdk. 웹의 개요
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869768"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="9873e-103">ASP.NET Core 웹 SDK</span><span class="sxs-lookup"><span data-stu-id="9873e-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="9873e-104">개요</span><span class="sxs-lookup"><span data-stu-id="9873e-104">Overview</span></span>

<span data-ttu-id="9873e-105">`Microsoft.NET.Sdk.Web`은 ASP.NET Core 앱을 빌드하기 위한 [MSBuild 프로젝트 SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) 입니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="9873e-106">이 SDK를 사용 하지 않고 ASP.NET Core 앱을 빌드할 수는 있지만 웹 SDK는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="9873e-107">최고 수준의 환경 제공에 맞게 조정 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="9873e-108">대부분의 사용자에 게 권장 되는 대상입니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-108">The recommended target for most users.</span></span>

<span data-ttu-id="9873e-109">프로젝트에서 웹 SDK를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="9873e-110">웹 SDK를 사용 하 여 사용 하도록 설정 된 기능:</span><span class="sxs-lookup"><span data-stu-id="9873e-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="9873e-111">.NET Core 3.0 이상을 대상으로 하는 프로젝트는 암시적으로 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="9873e-112">[ASP.NET Core 공유 프레임 워크](xref:fundamentals/metapackage-app)입니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="9873e-113">ASP.NET Core 앱을 빌드하기 위해 설계 된 [분석기](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 입니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="9873e-114">웹 SDK는 WebDeploy를 사용 하 여 게시 프로필 및 게시를 사용할 수 있도록 하는 MSBuild 대상을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="9873e-115">속성</span><span class="sxs-lookup"><span data-stu-id="9873e-115">Properties</span></span>

| <span data-ttu-id="9873e-116">속성</span><span class="sxs-lookup"><span data-stu-id="9873e-116">Property</span></span> | <span data-ttu-id="9873e-117">설명</span><span class="sxs-lookup"><span data-stu-id="9873e-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="9873e-118">`Microsoft.AspNetCore.App` 공유 프레임 워크에 대 한 암시적 참조를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="9873e-119">ASP.NET Core 분석기에 대 한 암시적 참조를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="9873e-120">Blazor (서버) 응용 프로그램을 빌드할 때 Razor 구성 요소 분석기에 대 한 암시적 참조를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9873e-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
