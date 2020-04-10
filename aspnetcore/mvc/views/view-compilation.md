---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994602"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="f3630-103">ASP.NET Core의 Razor 파일 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3630-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="f3630-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3630-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3630-105">확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f3630-106">애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="f3630-107">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3630-107">Razor compilation</span></span>

<span data-ttu-id="f3630-108">Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f3630-109">런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 해당 파일이 갱신될 수 있도록 빌드 시 컴파일을 보완합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f3630-110">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3630-110">Runtime compilation</span></span>

<span data-ttu-id="f3630-111">모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="f3630-112">[Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="f3630-113">`AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="f3630-114">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="f3630-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="f3630-115">조건부로 런타임 컴파일을 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="f3630-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="f3630-116">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="f3630-117">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="f3630-118">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-118">Uses compiled views.</span></span>
* <span data-ttu-id="f3630-119">크기가 더 작습니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-119">Is smaller in size.</span></span>
* <span data-ttu-id="f3630-120">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="f3630-121">환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="f3630-122">활성 `Configuration` 값에 따라 조건부로 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="f3630-123">`AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="f3630-124">`ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `AddRazorRuntimeCompilation`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="f3630-125">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f3630-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3630-126">Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="f3630-127">Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="f3630-128">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3630-128">Razor compilation</span></span>

<span data-ttu-id="f3630-129">Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f3630-130">업데이트된 후에 Razor 파일을 편집하는 작업은 빌드 시 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="f3630-131">기본적으로 Razor 파일을 컴파일하는 데 필요한 컴파일된 *Views.dll*(*.cshtml* 파일 없음) 또는 참조 어셈블리만 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f3630-132">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="f3630-133">[Razor Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="f3630-134">Razor SDK는 미리 컴파일 특정 속성이 프로젝트 파일에서 설정되지 않은 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="f3630-135">예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f3630-136">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3630-136">Runtime compilation</span></span>

<span data-ttu-id="f3630-137">빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="f3630-138">*.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="f3630-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3630-139">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f3630-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end