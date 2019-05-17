---
title: ASP.NET Core 2.1 이상용 Microsoft.AspNetCore.App 메타패키지
author: Rick-Anderson
description: Microsoft.AspNetCore.App 메타패키지에는 지원되는 모든 ASP.NET Core 및 Entity Framework Core 패키지가 포함됩니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/21/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 913e3d83fbf1af7ea995a88202f86c60b359a7e2
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085660"
---
# <a name="microsoftaspnetcoreapp-metapackage-for-aspnet-core-21-or-later"></a><span data-ttu-id="b5975-103">ASP.NET Core 2.1 이상용 Microsoft.AspNetCore.App 메타패키지</span><span class="sxs-lookup"><span data-stu-id="b5975-103">Microsoft.AspNetCore.App metapackage for ASP.NET Core 2.1 or later</span></span>

<span data-ttu-id="b5975-104">이 기능을 사용하려면 .NET Core 2.1 이상을 대상으로 하는 ASP.NET Core 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-104">This feature requires ASP.NET Core 2.1 or later targeting .NET Core 2.1 or later.</span></span>

<span data-ttu-id="b5975-105">ASP.NET Core용 [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [메타패키지](/dotnet/core/packages#metapackages):</span><span class="sxs-lookup"><span data-stu-id="b5975-105">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="b5975-106">[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) 및 [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/)를 제외한 타사 종속성을 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-106">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="b5975-107">이러한 타사 종속성은 주요 프레임워크 기능이 작동하도록 하는 데 필요한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-107">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="b5975-108">이전에 언급한 것 이외의 타사 종속성을 포함하는 것을 제외하고 ASP.NET Core 팀에서 지원하는 모든 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-108">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="b5975-109">이전에 언급한 것 이외의 타사 종속성을 포함하는 것을 제외하고 Entity Framework 팀에서 지원하는 모든 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-109">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="b5975-110">ASP.NET Core 2.1 이상 Entity Framework Core 2.1 이상의 모든 기능은 `Microsoft.AspNetCore.App` 패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-110">All the features of ASP.NET Core 2.1 and later and Entity Framework Core 2.1 and later are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="b5975-111">ASP.NET Core 2.1 이상을 대상으로 하는 기본 프로젝트 템플릿에는 이 패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-111">The default project templates targeting ASP.NET Core 2.1 and later use this package.</span></span> <span data-ttu-id="b5975-112">ASP.NET Core 2.1 이상과 Entity Framework Core 2.1 이상을 대상으로 하는 애플리케이션은 `Microsoft.AspNetCore.App` 패키지를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-112">We recommend applications targeting ASP.NET Core 2.1 and later and Entity Framework Core 2.1 and later use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="b5975-113">`Microsoft.AspNetCore.App` 메타패키지의 버전 번호는 최소 ASP.NET Core 버전 및 Entity Framework Core 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-113">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="b5975-114">`Microsoft.AspNetCore.App` 메타패키지 사용 시에는 앱을 보호하기 위한 버전 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-114">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="b5975-115">`Microsoft.AspNetCore.App`에 패키지에 대한 전이적인(직접적이지 않은) 종속성을 가진 패키지가 포함되어 있고, 이러한 버전 번호가 서로 다를 경우 NuGet에서 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-115">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="b5975-116">앱에 추가된 다른 패키지는 `Microsoft.AspNetCore.App`에 포함된 패키지 버전을 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-116">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="b5975-117">버전 일관성은 안정적인 환경을 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-117">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="b5975-118">`Microsoft.AspNetCore.App`은 동일한 앱에서 함께 사용되는 관련 비트의 테스트되지 않은 버전 조합을 방지하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-118">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="b5975-119">`Microsoft.AspNetCore.App` 메타패키지를 사용하는 애플리케이션은 ASP.NET Core 공유 프레임워크를 자동으로 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-119">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="b5975-120">`Microsoft.AspNetCore.App` 메타패키지를 사용할 경우, 참조되는 ASP.NET Core NuGet 패키지의 자산이 애플리케이션을 사용하여 배포되지 **않습니다**. 이러한 자산은 &mdash; ASP.NET Core 공유 프레임워크에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-120">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="b5975-121">공유 프레임워크의 자산은 애플리케이션 시작 시간 단축을 위해 미리 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-121">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="b5975-122">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-122">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="b5975-123">다음 프로젝트 파일은 ASP.NET Core용 `Microsoft.AspNetCore.App` 메타패키지를 참조하며 일반적인 ASP.NET Core 2.1 템플릿을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-123">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.1 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="b5975-124">이전의 표시는 일반적인 ASP.NET Core 2.1 이상의 템플릿을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-124">The preceding markup represents a typical ASP.NET Core 2.1 and later template.</span></span> <span data-ttu-id="b5975-125">`Microsoft.AspNetCore.App` 패키지 참조의 버전 번호는 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-125">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="b5975-126">버전이 지정되지 않은 경우 SDK에서 [암시적](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) 버전, 즉 `Microsoft.NET.Sdk.Web`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-126">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="b5975-127">SDK에서 지정하는 암시적 버전을 사용하고, 패키지 참조에 버전 번호를 명시적으로 설정하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-127">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="b5975-128">이 접근 방식에 대한 질문이 있는 경우 GitHub의 [Microsoft.AspNetCore.App 암시적 버전에 대한 토론](https://github.com/aspnet/AspNetCore.Docs/issues/6430)에 의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-128">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="b5975-129">휴대용 앱의 암시적 버전은 `major.minor.0`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-129">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="b5975-130">공유 프레임워크 롤포워드 메커니즘은 설치된 공유 프레임워크 중 최신 호환 버전에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-130">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="b5975-131">개발, 테스트 및 프로덕션에서 동일한 버전이 사용되도록 하려면 모든 환경에 동일한 버전의 공유 프레임워크를 설치하도록 하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-131">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="b5975-132">자체 포함 앱의 경우 암시적 버전 번호가 설치된 SDK에 포함된 공유 프레임워크의 `major.minor.patch`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-132">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="b5975-133">`Microsoft.AspNetCore.App` 참조에 버전 번호를 지정해도 해당 버전의 고유 프레임워크가 선택된다고 보장할 수 **없습니다**.</span><span class="sxs-lookup"><span data-stu-id="b5975-133">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="b5975-134">예를 들어 "2.1.1" 버전을 지정했는데 "2.1.3"이 설치되는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-134">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="b5975-135">이 경우 앱은 "2.1.3"을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-135">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="b5975-136">권장되는 방법은 아니지만 롤포워드를 비활성화할 수 있습니다(패치 및/또는 부 버전).</span><span class="sxs-lookup"><span data-stu-id="b5975-136">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="b5975-137">DotNet 호스트 롤포워드 및 이 동작을 구성하는 방법에 대한 자세한 내용은 [DotNet 호스트 롤포워드](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-137">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b5975-138">암시적 버전 `Microsoft.AspNetCore.App`을 사용하려면 `<Project Sdk`를 `Microsoft.NET.Sdk.Web`으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-138">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="b5975-139">`<Project Sdk="Microsoft.NET.Sdk">`가 후행 `.Web` 없이 사용되는 경우:</span><span class="sxs-lookup"><span data-stu-id="b5975-139">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="b5975-140">다음 경고가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-140">The following warning is generated:</span></span>

  <span data-ttu-id="b5975-141">*경고 NU1604: 프로젝트 종속성 Microsoft.AspNetCore.App에 포함 하한이 포함되어 있지 않습니다. 일관된 복원 결과를 얻으려면 종속 버전의 하한을 포함하세요.*</span><span class="sxs-lookup"><span data-stu-id="b5975-141">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="b5975-142">이것은 .NET Core 2.1 SDK의 알려진 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-142">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="b5975-143">ASP.NET Core 업데이트</span><span class="sxs-lookup"><span data-stu-id="b5975-143">Update ASP.NET Core</span></span>

<span data-ttu-id="b5975-144">`Microsoft.AspNetCore.App` [메타패키지](/dotnet/core/packages#metapackages)는 NuGet에서 업데이트된 기존 패키지가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-144">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="b5975-145">`Microsoft.NETCore.App`과 유사한 `Microsoft.AspNetCore.App`은 공유 런타임을 나타내며, NuGet 외부에서 처리되는 특별한 버전 관리 의미 체계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-145">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="b5975-146">자세한 내용은 [패키지, 메타패키지 및 프레임워크](/dotnet/core/packages)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-146">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="b5975-147">ASP.NET Core를 업데이트하려면:</span><span class="sxs-lookup"><span data-stu-id="b5975-147">To update ASP.NET Core:</span></span>

* <span data-ttu-id="b5975-148">개발 머신 및 빌드 서버: [.NET Core SDK](https://www.microsoft.com/net/download)를 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-148">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="b5975-149">배포 서버: [.NET Core 런타임](https://www.microsoft.com/net/download)을 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-149">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="b5975-150">애플리케이션을 다시 시작하면 애플리케이션이 최신 설치 버전으로 롤포워드됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-150">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="b5975-151">프로젝트 파일에서 `Microsoft.AspNetCore.App` 버전 번호를 업데이트할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b5975-151">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="b5975-152">자세한 내용은 [Framework 종속 앱 롤포워드](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-152">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="b5975-153">애플리케이션에서 이전에 `Microsoft.AspNetCore.All`을 사용한 경우 [Microsoft.AspNetCore.All에서 Microsoft.AspNetCore.App으로 마이그레이션](xref:fundamentals/metapackage#migrate)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b5975-153">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>
