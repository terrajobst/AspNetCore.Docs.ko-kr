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
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a>ASP.NET Core [!OP.NO-LOC(Blazor)]용 링커 구성

[Luke Latham](https://github.com/guardrex)으로

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!OP.NO-LOC(Blazor)]은(는) 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 제거합니다.

다음 방법 중 하나를 사용하여 어셈블리 연결을 제어합니다.

* [MSBuild 속성](#disable-linking-with-a-msbuild-property)을 사용하여 전역적으로 연결을 사용하지 않도록 설정합니다.
* [구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.

## <a name="disable-linking-with-a-msbuild-property"></a>MSBuild 속성을 사용하여 연결을 사용하지 않도록 설정

게시를 포함하는 앱이 빌드되면 연결이 기본적으로 활성화됩니다. 모든 어셈블리에 대한 연결을 비활성화하려면 프로젝트 파일에서 `BlazorLinkOnBuild` MSBuild 속성을 `false`로 설정합니다.

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>구성 파일과의 연결 제어

XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker.xml*:

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

자세한 내용은 [IL 링커: Xml 설명자 구문](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)을 참조하세요.

### <a name="configure-the-linker-for-internationalization"></a>국제화를 위한 링커 구성

기본적으로 [!OP.NO-LOC(Blazor)] WebAssembly 앱에 대한 [!OP.NO-LOC(Blazor)]의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다. 이 어셈블리를 제거하면 앱 크기를 최소화합니다.

유지되는 I18N 어셈블리 종류를 제어하려면 프로젝트 파일에서 `<MonoLinkerI18NAssemblies>` MSBuild 속성을 설정합니다.

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| 지역 값     | Mono 지역 어셈블리    |
| ---------------- | ----------------------- |
| `all`            | 포함된 모든 어셈블리 |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`(기본값) | 없음                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

여러 값을 지정할 경우 쉼표를 사용하여 구분합니다(예: `mideast,west`).

자세한 내용은 [I18N: Pnetlib 국제화 프레임워크 라이브러리(mono/mono GitHub 리포지토리)](https://github.com/mono/mono/tree/master/mcs/class/I18N)를 참조하세요.
