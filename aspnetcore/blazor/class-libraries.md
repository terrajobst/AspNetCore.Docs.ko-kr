---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 Blazor 앱에 구성 요소를 포함 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 32088b43f91174596f6b9251d36782e806f966b9
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213251"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="5c125-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="5c125-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="5c125-104">[Simon](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="5c125-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="5c125-105">구성 요소는 프로젝트 간에 [RCL (Razor 클래스 라이브러리)](xref:razor-pages/ui-class) 에서 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="5c125-106">*Razor 구성 요소 클래스 라이브러리* 는 다음에서 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="5c125-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="5c125-107">Another project in the solution.</span></span>
* <span data-ttu-id="5c125-108">NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-108">A NuGet package.</span></span>
* <span data-ttu-id="5c125-109">참조 된 .NET 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-109">A referenced .NET library.</span></span>

<span data-ttu-id="5c125-110">구성 요소가 일반적인 .NET 형식인 것 처럼 RCL에서 제공 하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="5c125-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="5c125-111">Create an RCL</span></span>

<span data-ttu-id="5c125-112"><xref:blazor/get-started> 문서의 지침에 따라 Blazor에 대 한 환경을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c125-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c125-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5c125-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-114">Create a new project.</span></span>
1. <span data-ttu-id="5c125-115">**Razor 클래스 라이브러리**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="5c125-116">**다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-116">Select **Next**.</span></span>
1. <span data-ttu-id="5c125-117">**새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="5c125-118">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="5c125-119">이 항목의 예제에서는 `MyComponentLib1`프로젝트 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="5c125-120">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-120">Select **Create**.</span></span>
1. <span data-ttu-id="5c125-121">솔루션에 RCL을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="5c125-122">솔루션을 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-122">Right-click the solution.</span></span> <span data-ttu-id="5c125-123">**기존 프로젝트** > **추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="5c125-124">RCL의 프로젝트 파일로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="5c125-125">RCL의 프로젝트 파일 ( *.csproj*)을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="5c125-126">앱에서 RCL 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="5c125-127">앱 프로젝트를 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-127">Right-click the app project.</span></span> <span data-ttu-id="5c125-128"> > **참조** **추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="5c125-129">RCL 프로젝트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-129">Select the RCL project.</span></span> <span data-ttu-id="5c125-130">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="5c125-131">템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란이 선택 되어 있는 경우 razor 구성 요소 작성을 사용 하려면 다음 내용을 포함 하는 생성 된 프로젝트의 루트에 *_Imports razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="5c125-132">생성 된 프로젝트의 루트를 수동으로 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5c125-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c125-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="5c125-134">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 **Razor 클래스 라이브러리** 템플릿 (`razorclasslib`)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="5c125-135">다음 예제에서는 `MyComponentLib1`라는 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="5c125-136">`MyComponentLib1`를 보유 하는 폴더는 명령이 실행 될 때 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="5c125-137">템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용 하는 경우 Razor 구성 요소 작성을 사용 하려면 다음 내용을 포함 하는 생성 된 프로젝트의 루트에 *_Imports razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="5c125-138">생성 된 프로젝트의 루트를 수동으로 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="5c125-139">기존 프로젝트에 라이브러리를 추가 하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="5c125-140">다음 예제에서는 RCL이 앱에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="5c125-141">라이브러리에 대 한 경로를 사용 하 여 앱의 프로젝트 폴더에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="5c125-142">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5c125-142">Consume a library component</span></span>

<span data-ttu-id="5c125-143">다른 프로젝트의 라이브러리에 정의 된 구성 요소를 사용 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="5c125-144">전체 형식 이름에 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="5c125-145">Razor의 [\@using](xref:mvc/views/razor#using) 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="5c125-146">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-146">Individual components can be added by name.</span></span>

<span data-ttu-id="5c125-147">다음 예에서는 `MyComponentLib1` `SalesReport` 구성 요소를 포함 하는 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="5c125-148">네임 스페이스에서 전체 형식 이름을 사용 하 여 `SalesReport` 구성 요소를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="5c125-149">`@using` 지시어를 사용 하 여 라이브러리를 범위로 가져오는 경우 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="5c125-150">최상위 *_Import razor* 파일에 `@using MyComponentLib1` 지시어를 포함 하 여 전체 프로젝트에서 라이브러리의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="5c125-151">모든 수준의 *_Import razor* 파일에 지시문을 추가 하 여 폴더 내의 단일 페이지 또는 페이지 집합에 네임 스페이스를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="5c125-152">빌드, 팩 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="5c125-152">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="5c125-153">구성 요소 라이브러리는 표준 .NET 라이브러리 이기 때문에 NuGet으로 패키징 및 전달 하는 것은 nuget에 라이브러리를 패키징 및 전달 하는 것과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-153">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="5c125-154">패키지는 명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-154">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="5c125-155">명령 셸에서 [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용 하 여 NuGet에 패키지를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-155">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="5c125-156">정적 자산을 사용 하 여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="5c125-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="5c125-157">RCL에는 정적 자산이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-157">An RCL can include static assets.</span></span> <span data-ttu-id="5c125-158">정적 자산은 라이브러리를 사용 하는 모든 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c125-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="5c125-159">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c125-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c125-160">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5c125-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
