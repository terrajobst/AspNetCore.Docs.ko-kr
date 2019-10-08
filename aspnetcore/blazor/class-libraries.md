---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 Blazor apps에 구성 요소를 포함 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/class-libraries
ms.openlocfilehash: 2e042b43c6db24e0ecac727be100575fe1275e17
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999779"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="71172-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="71172-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="71172-104">[Simon](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="71172-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="71172-105">구성 요소는 프로젝트 간에 [RCL (Razor 클래스 라이브러리)](xref:razor-pages/ui-class) 에서 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="71172-106">*Razor 구성 요소 클래스 라이브러리* 는 다음에서 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="71172-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="71172-107">Another project in the solution.</span></span>
* <span data-ttu-id="71172-108">NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="71172-108">A NuGet package.</span></span>
* <span data-ttu-id="71172-109">참조 된 .NET 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="71172-109">A referenced .NET library.</span></span>

<span data-ttu-id="71172-110">구성 요소가 일반적인 .NET 형식인 것 처럼 RCL에서 제공 하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="71172-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="71172-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="71172-111">Create an RCL</span></span>

<span data-ttu-id="71172-112">@No__t-0 문서의 지침에 따라 Blazor 환경을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71172-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71172-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="71172-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="71172-114">Create a new project.</span></span>
1. <span data-ttu-id="71172-115">**Razor 클래스 라이브러리**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="71172-116">**다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-116">Select **Next**.</span></span>
1. <span data-ttu-id="71172-117">**새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="71172-118">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="71172-119">이 항목의 예제에서는 프로젝트 이름 `MyComponentLib1`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="71172-120">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-120">Select **Create**.</span></span>
1. <span data-ttu-id="71172-121">솔루션에 RCL을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="71172-122">솔루션을 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-122">Right-click the solution.</span></span> <span data-ttu-id="71172-123">@No__t**기존 프로젝트**1 **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="71172-124">RCL의 프로젝트 파일로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="71172-125">RCL의 프로젝트 파일 ( *.csproj*)을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="71172-126">앱에서 RCL 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="71172-127">앱 프로젝트를 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-127">Right-click the app project.</span></span> <span data-ttu-id="71172-128">@No__t **추가** **참조**추가를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="71172-129">RCL 프로젝트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-129">Select the RCL project.</span></span> <span data-ttu-id="71172-130">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="71172-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="71172-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="71172-132">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 **Razor 클래스 라이브러리** 템플릿 (`razorclasslib`)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="71172-133">다음 예제에서는 `MyComponentLib1` 이라는 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="71172-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="71172-134">@No__t를 포함 하는 폴더는 명령이 실행 될 때 자동으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="71172-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="71172-135">기존 프로젝트에 라이브러리를 추가 하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="71172-136">다음 예제에서는 RCL이 앱에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="71172-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="71172-137">라이브러리에 대 한 경로를 사용 하 여 앱의 프로젝트 폴더에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="71172-138">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="71172-138">Consume a library component</span></span>

<span data-ttu-id="71172-139">다른 프로젝트의 라이브러리에 정의 된 구성 요소를 사용 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="71172-140">전체 형식 이름에 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="71172-141">Razor의 [\@using](xref:mvc/views/razor#using) 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="71172-142">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-142">Individual components can be added by name.</span></span>

<span data-ttu-id="71172-143">다음 예제에서 `MyComponentLib1`은 `SalesReport` 구성 요소를 포함 하는 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="71172-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="71172-144">@No__t-0 구성 요소는 네임 스페이스를 사용 하 여 전체 형식 이름을 사용 하 여 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="71172-145">@No__t-0 지시어를 사용 하 여 라이브러리를 범위로 가져오는 경우 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="71172-146">전체 프로젝트에서 라이브러리의 구성 요소를 사용할 수 있도록 하려면 `@using MyComponentLib1` 지시문을 최상위 Import-module 파일에 포함 합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="71172-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="71172-147">모든 수준에서 *_Import. razor* 파일에 지시문을 추가 하 여 폴더 내의 단일 페이지 또는 페이지 집합에 네임 스페이스를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="71172-148">빌드, 팩 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="71172-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="71172-149">구성 요소 라이브러리는 표준 .NET 라이브러리 이기 때문에 NuGet으로 패키징 및 전달 하는 것은 nuget에 라이브러리를 패키징 및 전달 하는 것과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="71172-150">패키지는 명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="71172-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="71172-151">명령 셸에서 [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용 하 여 NuGet에 패키지를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="71172-151">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="71172-152">정적 자산을 사용 하 여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="71172-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="71172-153">RCL에는 정적 자산이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-153">An RCL can include static assets.</span></span> <span data-ttu-id="71172-154">정적 자산은 라이브러리를 사용 하는 모든 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="71172-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="71172-155">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="71172-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71172-156">추가 자료</span><span class="sxs-lookup"><span data-stu-id="71172-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
