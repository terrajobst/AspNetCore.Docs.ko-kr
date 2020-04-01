---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 구성 요소를 Blazor 앱에 포함하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218768"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="440c2-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="440c2-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="440c2-104">작성자: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="440c2-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="440c2-105">프로젝트 간에 [RCL](xref:razor-pages/ui-class)(Razor 클래스 라이브러리)의 구성 요소를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="440c2-106">다음 위치에서 *Razor 구성 요소 클래스 라이브러리*를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="440c2-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="440c2-107">Another project in the solution.</span></span>
* <span data-ttu-id="440c2-108">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="440c2-108">A NuGet package.</span></span>
* <span data-ttu-id="440c2-109">참조된 .NET 라이브러리</span><span class="sxs-lookup"><span data-stu-id="440c2-109">A referenced .NET library.</span></span>

<span data-ttu-id="440c2-110">구성 요소가 일반적인 .NET 형식인 것처럼, RCL에서 제공하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="440c2-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="440c2-111">Create an RCL</span></span>

<span data-ttu-id="440c2-112"><xref:blazor/get-started> 문서의 지침에 따라 사용자 환경을 Blazor에 맞게 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="440c2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="440c2-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="440c2-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-114">Create a new project.</span></span>
1. <span data-ttu-id="440c2-115">**Razor 클래스 라이브러리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="440c2-116">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-116">Select **Next**.</span></span>
1. <span data-ttu-id="440c2-117">**새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="440c2-118">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="440c2-119">이 항목의 예제에서는 프로젝트 이름으로 `MyComponentLib1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="440c2-120">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-120">Select **Create**.</span></span>
1. <span data-ttu-id="440c2-121">솔루션에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="440c2-122">솔루션을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-122">Right-click the solution.</span></span> <span data-ttu-id="440c2-123">**추가** > **기존 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="440c2-124">RCL의 프로젝트 파일로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="440c2-125">RCL의 프로젝트 파일( *.csproj*)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="440c2-126">앱에서 RCL 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="440c2-127">앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-127">Right-click the app project.</span></span> <span data-ttu-id="440c2-128">**추가** > **참조**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="440c2-129">RCL 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-129">Select the RCL project.</span></span> <span data-ttu-id="440c2-130">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="440c2-131">템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란을 선택한 경우 다음 내용을 포함하는 *_Imports.razor* 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="440c2-132">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="440c2-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="440c2-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="440c2-134">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 통해 **Razor 클래스 라이브러리** 템플릿(`razorclasslib`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="440c2-135">다음 예제에서는 이름이 `MyComponentLib1`인 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="440c2-136">명령을 실행하면 `MyComponentLib1`을 포함하는 폴더가 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="440c2-137">템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용한 경우 다음 내용을 포함하는 *_Imports.razor* 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="440c2-138">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="440c2-139">기존 프로젝트에 라이브러리를 추가하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="440c2-140">다음 예제에서는 앱에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="440c2-141">라이브러리 경로를 사용하여 앱의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="440c2-142">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="440c2-142">Consume a library component</span></span>

<span data-ttu-id="440c2-143">다른 프로젝트의 라이브러리에서 정의된 구성 요소를 사용하려면 다음 방법의 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="440c2-144">네임스페이스를 포함하는 전체 형식 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="440c2-145">Razor [\@using](xref:mvc/views/razor#using) 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="440c2-146">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-146">Individual components can be added by name.</span></span>

<span data-ttu-id="440c2-147">다음 예제에서 `MyComponentLib1`은 `SalesReport` 구성 요소가 포함된 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="440c2-148">네임스페이스를 포함하는 전체 형식 이름을 사용하여 `SalesReport` 구성 요소를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="440c2-149">`@using` 지시문을 사용하여 라이브러리를 범위로 가져온 경우 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="440c2-150">최상위 *_Import.razor* 파일에 `@using MyComponentLib1` 지시문을 포함하여 전체 프로젝트에서 라이브러리 구성 요소를 사용할 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="440c2-151">임의 수준의 *_Import.razor* 파일에 지시문을 추가하여 폴더 내의 단일 페이지 또는 페이지 집합에 네임스페이스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="440c2-152">정적 자산을 사용하여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="440c2-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="440c2-153">RCL에는 정적 자산이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-153">An RCL can include static assets.</span></span> <span data-ttu-id="440c2-154">정적 자산은 라이브러리를 사용하는 모든 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="440c2-155">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="440c2-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="440c2-156">빌드, 패키지 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="440c2-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="440c2-157">구성 요소 라이브러리는 표준 .NET 라이브러리이기 때문에 패키지하여 NuGet에 제공하는 과정이 라이브러리를 패키지하여 NuGet에 제공하는 과정과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="440c2-158">명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 패키지합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="440c2-159">명령 셸에서 [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용하여 패키지를 NuGet에 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="440c2-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="440c2-160">추가 자료</span><span class="sxs-lookup"><span data-stu-id="440c2-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="440c2-161">라이브러리에 XML 링커 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="440c2-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
