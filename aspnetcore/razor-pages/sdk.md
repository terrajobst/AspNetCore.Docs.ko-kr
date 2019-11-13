---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor 페이지를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963258"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>개요

[!INCLUDE[](~/includes/2.1-SDK.md)] `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK)를 포함 합니다. Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* 는 MVC 기반 또는 [Blazor](xref:blazor/index) 프로젝트 ASP.NET Core [Razor](xref:mvc/views/razor) 파일이 포함 된 프로젝트를 빌드, 패키징 및 게시 하는 데 필요 합니다.
* Razor (*cshtml* 또는 *razor*) 파일의 컴파일을 사용자 지정할 수 있는 미리 정의 된 대상, 속성 및 항목 집합을 포함 합니다.

Razor SDK에는 `Include` 특성이 `**\*.cshtml` 및 `**\*.razor` 와일드 카드 사용 패턴으로 설정 된 `Content` 항목이 포함 되어 있습니다. 일치 파일이 게시 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.
* Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.

Razor SDK에는 `Include` 특성이 `**\*.cshtml` 와일드 카드 사용 패턴으로 설정 된 `Content` 항목이 포함 되어 있습니다. 일치 파일이 게시 됩니다.

::: moniker-end

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Razor SDK 사용

대부분의 웹 앱은 Razor SDK를 명시적으로 참조할 필요가 없습니다.

::: moniker range=">= aspnetcore-3.0"

Razor SDK를 사용 하 여 Razor 뷰나 Razor Pages를 포함 하는 클래스 라이브러리를 빌드하려면 먼저 RCL (Razor 클래스 라이브러리) 프로젝트 템플릿을 사용 하는 것이 좋습니다. Blazor (*razor*) 파일을 빌드하는 데 사용 되는 Rcl은 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 패키지에 대 한 참조를 최소한으로 필요로 합니다. Razor 뷰나 페이지 (*cshtml* 파일)를 작성 하는 데 사용 되는 rcl은 `netcoreapp3.0` 이상을 대상으로 지정 하 고 해당 프로젝트 파일에서 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 `FrameworkReference`를 사용 해야 합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor SDK를 사용하여 Razor 보기 또는 Razor 페이지를 포함하는 클래스 라이브러리를 빌드하려면:

* `Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 일반적으로 `Microsoft.AspNetCore.Mvc`에 대 한 패키지 참조는 Razor Pages 및 Razor 뷰를 빌드하고 컴파일하는 데 필요한 추가 종속성을 수신 하는 데 필요 합니다. 프로젝트는 최소한 다음에 대 한 패키지 참조를 추가 해야 합니다.

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  `Microsoft.AspNetCore.Razor.Design` 패키지는 프로젝트에 대 한 Razor 컴파일 작업 및 대상을 제공 합니다.

  이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다. 다음 태그는 Razor SDK를 사용 하 여 ASP.NET Core Razor Pages 앱에 대 한 Razor 파일을 빌드하는 프로젝트 파일을 보여 줍니다.
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` 및 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다. 그러나 버전 감소 `Microsoft.AspNetCore.App` 패키지 참조는 `Microsoft.AspNetCore.Razor.Design`최신 버전을 포함 하지 않는 앱에 대 한 메타 패키지를 제공 합니다. 프로젝트는 Razor에 대 한 최신 빌드 시간 수정이 포함 되도록 `Microsoft.AspNetCore.Razor.Design` (또는 `Microsoft.AspNetCore.Mvc`)의 일관 된 버전을 참조 해야 합니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/aspnet/Razor/issues/2553)를 참조하세요.

::: moniker-end

### <a name="properties"></a>데이터 액세스

다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.

* `RazorCompileOnBuild` &ndash;를 `true`때 프로젝트를 빌드하는 과정에서 Razor 어셈블리를 컴파일하고 내보냅니다. 기본값은 `true`입니다.
* `RazorCompileOnPublish` &ndash;를 `true`때 프로젝트 게시의 일부로 Razor 어셈블리를 컴파일하고 내보냅니다. 기본값은 `true`입니다.

다음 표의 속성 및 항목은 Razor SDK에 대 한 입력 및 출력을 구성 하는 데 사용 됩니다.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ASP.NET Core 3.0부터 프로젝트 파일의 `RazorCompileOnBuild` 또는 `RazorCompileOnPublish` MSBuild 속성을 사용할 수 없는 경우 MVC 뷰 또는 Razor Pages는 기본적으로 제공 되지 않습니다. 앱이 런타임 컴파일을 사용 하 여 AspNetCore 파일을 처리 하는 경우 응용 프로그램에서 [RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) 패키지에 대 한 명시적 참조를 추가 해야 *합니다.*

::: moniker-end

| 항목 | 설명 |
| ----- | ----------- |
| `RazorGenerate` | 코드 생성에 대 한 입력 인 항목 요소 (*cshtml* 파일)입니다. |
| `RazorComponent` | Razor 구성 요소 코드 생성에 대 한 입력 인 항목 요소 (*razor* 파일)입니다. |
| `RazorCompile` | Razor 컴파일 대상에 대 한 입력 인 항목 요소 ( *.cs* 파일)입니다. 이 `ItemGroup`을 사용 하 여 Razor 어셈블리로 컴파일할 추가 파일을 지정 합니다. |
| `RazorTargetAssemblyAttribute` | 코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 포함 리소스로 추가 된 항목 요소는 생성 된 Razor 어셈블리에 포함 됩니다. |

| 속성 | 설명 |
| -------- | ----------- |
| `RazorTargetName` | Razor에서 생성한 어셈블리의 파일 이름(확장명 제외). | 
| `RazorOutputPath` | Razor 출력 디렉터리. |
| `RazorCompileToolset` | Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다. 유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 기본값은 `true`입니다. `true`경우 *web.config*, *json*및 *cshtml* 파일이 프로젝트의 콘텐츠로 포함 됩니다. `Microsoft.NET.Sdk.Web`를 통해 참조 되는 경우 *wwwroot* 및 config 파일 아래의 파일도 포함 됩니다. |
| `EnableDefaultRazorGenerateItems` | `true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다. |
| `GenerateRazorTargetAssemblyInfo` | `true`하면 `RazorAssemblyAttribute`에 의해 지정 된 특성이 포함 된 *.cs* 파일을 생성 하 고 컴파일 출력에 파일을 포함 합니다. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다. |
| `CopyRazorGenerateFilesToPublishDirectory` | `true`하면 `RazorGenerate` 항목 (*cshtml*) 파일을 게시 디렉터리로 복사 합니다. 일반적으로 빌드 타임 또는 게시 시간에 컴파일에 참여 하는 경우 게시 된 앱에 Razor 파일이 필요 하지 않습니다. 기본값은 `false`입니다. |
| `CopyRefAssembliesToPublishDirectory` | `true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다. 일반적으로 Razor 컴파일이 빌드 타임 또는 게시 시간에 발생 하는 경우 게시 된 앱에 대 한 참조 어셈블리는 필요 하지 않습니다. 게시 된 앱에 런타임 컴파일이 필요한 경우 `true`으로 설정 합니다. 예를 들어 응용 프로그램이 런타임에 cshtml 파일을 수정 하거나 포함 된 뷰를 사용 하는 경우 값을 `true`으로 설정 합니다 *.* 기본값은 `false`입니다. |
| `IncludeRazorContentInPack` | `true`하면 모든 Razor 콘텐츠 항목 (*cshtml* 파일)이 생성 된 NuGet 패키지에 포함 되도록 표시 됩니다. 기본값은 `false`입니다. |
| `EmbedRazorGenerateSources` | `true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml*) 항목을 추가합니다. 기본값은 `false`입니다. |
| `UseRazorBuildServer` | `true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다. 기본값은 `UseSharedCompilation`입니다. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true`경우 SDK는 런타임에 MVC에서 응용 프로그램 파트 검색을 수행 하는 데 사용 되는 추가 특성을 생성 합니다. |

속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.

### <a name="targets"></a>대상

Razor SDK는 두 기본 대상을 정의합니다.

* `RazorGenerate` &ndash; 코드는 `RazorGenerate` 항목 요소에서 *.cs* 파일을 생성 합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정 하려면 `RazorGenerateDependsOn` 속성을 사용 합니다.
* `RazorCompile` &ndash;은 생성 된 *.cs* 파일을 Razor 어셈블리로 컴파일합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정 하려면 `RazorCompileDependsOn`을 사용 합니다.
* `RazorComponentGenerate` &ndash; 코드는 `RazorComponent` 항목 요소에 대 한 *.cs* 파일을 생성 합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정 하려면 `RazorComponentGenerateDependsOn` 속성을 사용 합니다.

### <a name="runtime-compilation-of-razor-views"></a>Razor 뷰의 런타임 컴파일

* 기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다. 이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함). `CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.

* 웹 앱의 경우 앱이 `Microsoft.NET.Sdk.Web` SDK를 대상으로 하는지 확인 합니다.

## <a name="razor-language-version"></a>Razor 언어 버전

`Microsoft.NET.Sdk.Web` SDK를 대상으로 지정 하는 경우 응용 프로그램의 대상 프레임 워크 버전에서 Razor 언어 버전이 유추 됩니다. `Microsoft.NET.Sdk.Razor` SDK를 대상으로 하는 프로젝트 또는 응용 프로그램에 유추 된 값과 다른 Razor 언어 버전이 필요한 경우에는 앱의 프로젝트 파일에서 `<RazorLangVersion>` 속성을 설정 하 여 버전을 구성할 수 있습니다.

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor의 언어 버전은 작성 된 런타임의 버전과 긴밀 하 게 통합 됩니다. 런타임에 대해 설계 되지 않은 언어 버전을 대상으로 하는 것은 지원 되지 않으며 빌드 오류가 발생할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* [.NET Core용 csproj 형식에 대한 추가 사항](/dotnet/core/tools/csproj)
* [일반적인 MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)
