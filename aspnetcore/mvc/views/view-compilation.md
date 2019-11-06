---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0aa632bce32ef44f65d92639284c64c1d00e952e
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080818"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core의 Razor 파일 컴파일

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Razor 파일은 런타임 시에 관련 MVC 보기가 호출될 때 컴파일됩니다. 빌드 시 Razor 파일 게시는 지원되지 않습니다. Razor 파일은 필요에 따라 사전 컴파일 도구를 사용하여 게시 시에 컴파일되어 앱과 함께 배포될 수 있습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다. 빌드 시 Razor 파일 게시는 지원되지 않습니다. Razor 파일은 필요에 따라 사전 컴파일 도구를 사용하여 게시 시에 컴파일되어 앱과 함께 배포될 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다. Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시에 컴파일됩니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시에 컴파일됩니다. 응용 프로그램을 구성하여 런타임 컴파일을 선택적으로 활성시킬 수 있습니다.

::: moniker-end

## <a name="razor-compilation"></a>Razor 컴파일

::: moniker range=">= aspnetcore-3.0"

Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다. 런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 해당 파일이 갱신될 수 있도록 빌드 시 컴파일을 보완합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다. Razor 파일이 수정된 후에 편집하는 작업은 빌드 시에 지원됩니다. 기본적으로 *.cshtml* 파일을 제외한 컴파일된 *Views.dll* 또는 Razor 파일을 컴파일하는 데 필요한 참조 어셈블리만 앱과 함께 배포됩니다.

> [!IMPORTANT]
> 사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다. [Razor SDK](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.
>
> Razor SDK는 프로젝트 파일에 사전 컴파일 특정 속성이 설정되지 않은 경우에만 유효합니다. 예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성됩니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

프로젝트가 .NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 패키지를 설치하세요.

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

프로젝트가 .NET Core를 대상으로 하는 경우 변경 작업이 필요하지 않습니다.

ASP.NET Core 2.x 프로젝트 템플릿은 기본적으로 `MvcRazorCompileOnPublish` 속성을 암시적으로 `true`로 설정합니다. 따라서 이 요소는 *.csproj* 파일에서 안전하게 제거할 수 있습니다.

> [!IMPORTANT]
> 사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다. [Razor SDK](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.
>
> ASP.NET Core 2.0에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)를 수행하는 경우 Razor 파일 사전 컴파일은 사용 불가능합니다.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

`MvcRazorCompileOnPublish` 속성을 `true`로 설정하고 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 패키지를 설치하세요. 다음 *.csproj* 예제는 이러한 설정을 강조 표시합니다.

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[.NET Core CLI publish 명령](/dotnet/core/tools/dotnet-publish)을 사용하여 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)에 대한 앱을 준비합니다. 예를 들어 프로젝트 루트에서 다음 명령을 실행합니다.

```dotnetcli
dotnet publish -c Release
```

사전 컴파일에 성공하면 컴파일된 Razor 파일이 포함된 *\<project_name>.PrecompiledViews.dll* 파일이 생성됩니다. 예를 들어 아래 스크린샷은 *WebApplication1.PrecompiledViews.dll* 내부의 *Index.cshtml* 내용을 보여 줍니다.

![DLL 내의 Razor 보기](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>런타임 컴파일

::: moniker range="= aspnetcore-2.1"

빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다. *.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>는 디스크의 파일이 변경되면 Razor 파일(Razor 보기 및 Razor Pages)이 컴파일되고 갱신되는지 여부를 결정하는 값을 가져오거나 설정합니다.

다음의 경우 기본값은 `true`입니다.

* 앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 이하로 설정된 경우.
* 앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 이상으로 설정되고 앱이 개발 환경 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>에 있는 경우. 즉, Razor 파일은 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>가 명시적으로 설정되지 않는 한 비개발 환경에서 다시 컴파일되지 않습니다.

앱의 호환성 버전 설정에 대한 지침과 예제는 <xref:mvc/compatibility-version>을 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

런타임 컴파일은 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 패키지를 사용하여 활성화됩니다. 런타임 컴파일을 사용하려면 앱에서 다음 작업을 수행해야 합니다.

* [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.
* `AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 수정합니다.

  ```csharp
  services
      .AddControllersWithViews()
      .AddRazorRuntimeCompilation();
  ```

::: moniker-end

## <a name="additional-resources"></a>추가 자료

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
