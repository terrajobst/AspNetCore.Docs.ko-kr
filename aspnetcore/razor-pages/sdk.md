---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor 페이지를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: razor-pages/sdk
ms.openlocfilehash: 8c4e882af93b043afaa0bcf86fd1583405f84be9
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750172"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

합니다 [!INCLUDE[](~/includes/2.1-SDK.md)] 포함 된 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Razor SDK:

* ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.
* Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a>Razor SDK 사용

대부분의 웹 앱을 명시적으로 Razor SDK를 참조할 필요가 없습니다.

Razor SDK를 사용하여 Razor 보기 또는 Razor 페이지를 포함하는 클래스 라이브러리를 빌드하려면:

* `Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* 일반적으로 패키지에 대 한 참조 `Microsoft.AspNetCore.Mvc` Razor 페이지 및 Razor 뷰 컴파일 및 빌드하는 데 필요한 추가 종속성을 수신 해야 합니다. 최소한 프로젝트에 대 한 패키지 참조를 추가 해야 합니다.

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  `Microsoft.AspNetCore.Razor.Design` 패키지를 프로젝트에 대 한 Razor 컴파일 작업 및 대상에 제공 합니다.

  이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다. 다음 태그는 ASP.NET Core Razor 페이지 앱에 대 한 Razor 파일 작성 하려면 Razor SDK를 사용 하는 프로젝트 파일을 보여 줍니다.
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` 하 고 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지에 포함 된 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다. 그러나 버전-작음 `Microsoft.AspNetCore.App` 패키지 참조는 메타 패키지의 최신 버전을 포함 하지 않는 앱에 제공 `Microsoft.AspNetCore.Razor.Design`합니다. 프로젝트의 일관 된 버전을 참조 해야 합니다 `Microsoft.AspNetCore.Razor.Design` (또는 `Microsoft.AspNetCore.Mvc`) Razor에 대 한 최신 빌드 시간 수정 프로그램이 포함 되도록 합니다. 자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/Razor/issues/2553)합니다.

::: moniker-end

### <a name="properties"></a>속성

다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.

* `RazorCompileOnBuild` &ndash; 때 `true`, 컴파일하고 프로젝트 빌드의 일부로 Razor 어셈블리를 내보냅니다. 기본값은 `true`입니다.
* `RazorCompileOnPublish` &ndash; 때 `true`, 컴파일 및 Razor 어셈블리 프로젝트를 게시 하는 일환으로 내보냅니다. 기본값은 `true`입니다.

속성 및 다음 표에 항목을 입력 및 출력 Razor SDK를 구성 하려면 사용 됩니다.

| 항목 | 설명 |
| ----- | ----------- |
| `RazorGenerate` | 코드 생성 대상에 입력되는 항목 요소( *.cshtml* 파일). |
| `RazorCompile` | 항목 요소 ( *.cs* 파일)는 Razor 컴파일 대상에 입력 합니다. 이 ItemGroup을 사용하여 Razor 어셈블리에 컴파일할 추가 파일을 지정합니다. |
| `RazorTargetAssemblyAttribute` | 코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다. 예를 들어 다음과 같습니다.  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 항목 요소에서 생성 된 Razor 어셈블리에 포함 리소스로 추가 합니다. |

| 속성 | 설명 |
| -------- | ----------- |
| `RazorTargetName` | Razor에서 생성한 어셈블리의 파일 이름(확장명 제외). | 
| `RazorOutputPath` | Razor 출력 디렉터리. |
| `RazorCompileToolset` | Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다. 유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 기본값은 `true`입니다. 때 `true`를 포함 *web.config*, *.json*, 및 *.cshtml* 프로젝트의 내용으로는 파일입니다. 통해 참조 될 때 `Microsoft.NET.Sdk.Web`, 아래에 있는 파일 *wwwroot* 및 구성 파일도 포함 됩니다. |
| `EnableDefaultRazorGenerateItems` | `true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다. |
| `GenerateRazorTargetAssemblyInfo` | 때 `true`, 생성을 *.cs* 로 지정 된 특성을 포함 하는 파일 `RazorAssemblyAttribute` 컴파일 출력에 파일을 포함 합니다. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다. |
| `CopyRazorGenerateFilesToPublishDirectory` | 때 `true`, 복사본 `RazorGenerate` 항목 ( *.cshtml*) 파일을 게시 디렉터리입니다. 일반적으로 Razor 파일 컴파일 빌드 시간 또는 게시 시간에에서 참여 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다. 기본값은 `false`입니다. |
| `CopyRefAssembliesToPublishDirectory` | `true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다. 일반적으로 참조 어셈블리 Razor 컴파일 빌드 시간 또는 게시 시간에 발생 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다. 로 `true` 게시 된 앱에 런타임 컴파일이 필요 하면 합니다. 예를 들어, 값을 설정 `true` 앱을 수정 하는 경우 *.cshtml* 런타임에 파일 또는 포함 된 뷰를 사용 합니다. 기본값은 `false`입니다. |
| `IncludeRazorContentInPack` | 때 `true`, 모든 Razor 콘텐츠 항목 ( *.cshtml* 파일) 생성된 된 NuGet 패키지에 포함 되도록 표시 됩니다. 기본값은 `false`입니다. |
| `EmbedRazorGenerateSources` | `true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml*) 항목을 추가합니다. 기본값은 `false`입니다. |
| `UseRazorBuildServer` | `true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다. 기본값은 `UseSharedCompilation`입니다. |

속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.

### <a name="targets"></a>대상

Razor SDK는 두 기본 대상을 정의합니다.

* `RazorGenerate` &ndash; 코드 생성 *.cs* 에서 파일을 `RazorGenerate` 항목 요소가 있습니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorGenerateDependsOn` 속성을 사용합니다.
* `RazorCompile` &ndash; 생성 된 컴파일합니다 *.cs* Razor 어셈블리 파일입니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorCompileDependsOn`을 사용합니다.

### <a name="runtime-compilation-of-razor-views"></a>Razor 뷰의 런타임 컴파일

* 기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다. 이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함). `CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.

* 웹 앱에 대 한 앱을 대상으로 확인 된 `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Razor 언어 버전

대상으로 할 때를 `Microsoft.NET.Sdk.Web` SDK Razor 언어 버전에서 유추 되는 앱의 대상 프레임 워크 버전입니다. 프로젝트의 대상으로 하는 `Microsoft.NET.Sdk.Razor` SDK 또는 유추 된 값 보다 다른 Razor 언어 버전을 앱에 필요한는 드문 경우 버전을 설정 하 여 구성할 수 있습니다는 `<RazorLangVersion>` 앱의 프로젝트 파일에서 속성:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```
