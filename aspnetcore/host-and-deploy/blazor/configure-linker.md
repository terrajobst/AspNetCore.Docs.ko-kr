---
title: ASP.NET Core Blazor용 링커 구성
author: guardrex
description: Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 263b85a3213c1da233e4c96095faaf39d0a8e13f
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726770"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="fe780-103">ASP.NET Core [!OP.NO-LOC(Blazor)]용 링커 구성</span><span class="sxs-lookup"><span data-stu-id="fe780-103">Configure the Linker for ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

<span data-ttu-id="fe780-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="fe780-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!OP.NO-LOC(Blazor)]<span data-ttu-id="fe780-105">은(는) 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="fe780-106">다음 방법 중 하나를 사용하여 어셈블리 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="fe780-107">[MSBuild 속성](#disable-linking-with-a-msbuild-property)을 사용하여 전역적으로 연결을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="fe780-108">[구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="fe780-109">MSBuild 속성을 사용하여 연결을 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="fe780-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="fe780-110">게시를 포함하는 앱이 빌드되면 연결이 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="fe780-111">모든 어셈블리에 대한 연결을 비활성화하려면 프로젝트 파일에서 `BlazorLinkOnBuild` MSBuild 속성을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="fe780-112">구성 파일과의 연결 제어</span><span class="sxs-lookup"><span data-stu-id="fe780-112">Control linking with a configuration file</span></span>

<span data-ttu-id="fe780-113">XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="fe780-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="fe780-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or [!OP.NO-LOC(Blazor)] packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
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

<span data-ttu-id="fe780-115">자세한 내용은 [IL 링커: Xml 설명자 구문](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fe780-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="fe780-116">국제화를 위한 링커 구성</span><span class="sxs-lookup"><span data-stu-id="fe780-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="fe780-117">기본적으로 [!OP.NO-LOC(Blazor)] WebAssembly 앱에 대한 [!OP.NO-LOC(Blazor)]의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-117">By default, [!OP.NO-LOC(Blazor)]'s linker configuration for [!OP.NO-LOC(Blazor)] WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="fe780-118">이 어셈블리를 제거하면 앱 크기를 최소화합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="fe780-119">유지되는 I18N 어셈블리 종류를 제어하려면 프로젝트 파일에서 `<MonoLinkerI18NAssemblies>` MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fe780-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="fe780-120">지역 값</span><span class="sxs-lookup"><span data-stu-id="fe780-120">Region Value</span></span>     | <span data-ttu-id="fe780-121">Mono 지역 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fe780-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="fe780-122">포함된 모든 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fe780-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="fe780-123">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="fe780-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="fe780-124">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="fe780-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="fe780-125">`none`(기본값)</span><span class="sxs-lookup"><span data-stu-id="fe780-125">`none` (default)</span></span> | <span data-ttu-id="fe780-126">없음</span><span class="sxs-lookup"><span data-stu-id="fe780-126">None</span></span>                    |
| `other`          | <span data-ttu-id="fe780-127">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="fe780-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="fe780-128">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="fe780-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="fe780-129">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="fe780-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="fe780-130">여러 값을 지정할 경우 쉼표를 사용하여 구분합니다(예: `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="fe780-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="fe780-131">자세한 내용은 [I18N: Pnetlib 국제화 프레임워크 라이브러리(mono/mono GitHub 리포지토리)](https://github.com/mono/mono/tree/master/mcs/class/I18N)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fe780-131">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
