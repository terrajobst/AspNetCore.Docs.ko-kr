---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: cd096bba5eb580c0a606699a2bf7c36442fb56f7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652497"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core의 Razor 파일 컴파일

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Razor 파일은 런타임 시 관련 MVC 뷰가 호출되면 컴파일됩니다. 빌드 시 Razor 파일 게시는 지원되지 않습니다. Razor 파일은 필요에 따라 미리 컴파일 도구를 사용하여 앱 게시 및 배포 시 컴파일될 수 있습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다. 빌드 시 Razor 파일 게시는 지원되지 않습니다. Razor 파일은 필요에 따라 미리 컴파일 도구를 사용하여 앱 게시 및 배포 시 컴파일될 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다. Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시에 컴파일됩니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다. 애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.

::: moniker-end

## <a name="razor-compilation"></a>Razor 컴파일

::: moniker range=">= aspnetcore-3.0"

Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다. 런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 해당 파일이 갱신될 수 있도록 빌드 시 컴파일을 보완합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다. 업데이트된 후에 Razor 파일을 편집하는 작업은 빌드 시 지원됩니다. 기본적으로 Razor 파일을 컴파일하는 데 필요한 컴파일된 *Views.dll*( *.cshtml* 파일 없음) 또는 참조 어셈블리만 앱과 함께 배포됩니다.

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

[.NET Core CLI publish 명령](/dotnet/core/deploying/#framework-dependent-deployments-fdd)을 사용하여 [프레임워크 종속 배포](/dotnet/core/tools/dotnet-publish)에 대한 앱을 준비합니다. 예를 들어 프로젝트 루트에서 다음 명령을 실행합니다.

```dotnetcli
dotnet publish -c Release
```

사전 컴파일에 성공하면 컴파일된 Razor 파일이 포함된 *\<project_name>.PrecompiledViews.dll* 파일이 생성됩니다. 예를 들어 아래 스크린샷은 *WebApplication1.PrecompiledViews.dll* 내부의 *Index.cshtml*의 내용을 보여 줍니다.

![DLL 내의 Razor 보기](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>런타임 컴파일

::: moniker range="= aspnetcore-2.1"

빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다. *.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>는 디스크의 파일이 변경되면 Razor 파일(Razor 뷰 및 Razor Pages)이 컴파일되고 업데이트되는지 여부를 결정하는 값을 가져오거나 설정합니다.

다음의 경우 기본값은 `true`입니다.

* 앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 이하로 설정된 경우.
* 앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 이상으로 설정되고 앱이 개발 환경 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>에 있는 경우. 즉, Razor 파일은 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>가 명시적으로 설정되지 않는 한 비개발 환경에서 다시 컴파일되지 않습니다.

앱의 호환성 버전 설정에 대한 지침과 예제는 <xref:mvc/compatibility-version>을 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.

1. [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.

1. `Startup.ConfigureServices`에 대한 호출을 포함하도록 프로젝트의 `AddRazorRuntimeCompilation` 메서드를 업데이트합니다. 다음은 그 예입니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>조건부로 런타임 컴파일을 사용하도록 설정

런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다. 이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.

* 컴파일된 보기를 사용합니다.
* 크기가 더 작습니다.
* 프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.

환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.

1. 활성 [ 값에 따라 조건부로 ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`Configuration` 패키지를 참조합니다.

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. `Startup.ConfigureServices`에 대한 호출을 포함하도록 프로젝트의 `AddRazorRuntimeCompilation` 메서드를 업데이트합니다. `AddRazorRuntimeCompilation` 변수가 `ASPNETCORE_ENVIRONMENT`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `Development`을 실행합니다.

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

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
