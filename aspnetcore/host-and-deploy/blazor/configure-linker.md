---
title: ASP.NET Core Blazor용 링커 구성
author: guardrex
description: Blazor 앱을 빌드할 때 IL(Intermediate Language) 링커를 제어하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: d3dd69e263e88ca1fc301eefc0da186a023aa96f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211593"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="68f32-103">ASP.NET Core Blazor용 링커 구성</span><span class="sxs-lookup"><span data-stu-id="68f32-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="68f32-104">작성자: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68f32-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="68f32-105">Blazor는 릴리스 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="68f32-106">다음 방법 중 하나를 사용하여 어셈블리 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="68f32-107">[MSBuild 속성](#disable-linking-with-a-msbuild-property)을 사용하여 전역적으로 연결을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="68f32-108">[구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="68f32-109">MSBuild 속성을 사용하여 연결을 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="68f32-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="68f32-110">게시를 포함하는 앱이 빌드되면 릴리스 모드에서 연결이 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="68f32-111">모든 어셈블리에 대한 연결을 비활성화하려면 프로젝트 파일에서 `BlazorLinkOnBuild` MSBuild 속성을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="68f32-112">구성 파일과의 연결 제어</span><span class="sxs-lookup"><span data-stu-id="68f32-112">Control linking with a configuration file</span></span>

<span data-ttu-id="68f32-113">XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="68f32-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="68f32-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="68f32-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="68f32-115">자세한 내용은 [IL 링커: Xml 설명자 구문](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68f32-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>
