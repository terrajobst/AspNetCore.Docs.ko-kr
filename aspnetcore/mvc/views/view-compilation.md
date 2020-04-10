---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994602"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core의 Razor 파일 컴파일

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다. 애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.

## <a name="razor-compilation"></a>Razor 컴파일

Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다. 런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 해당 파일이 갱신될 수 있도록 빌드 시 컴파일을 보완합니다.

## <a name="runtime-compilation"></a>런타임 컴파일

모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.

1. [Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.

1. `AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다. 예를 들어:

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

1. 활성 `Configuration` 값에 따라 조건부로 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 패키지를 참조합니다.

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. `AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다. `ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `AddRazorRuntimeCompilation`을 실행합니다.

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

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다. Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 컴파일됩니다.

## <a name="razor-compilation"></a>Razor 컴파일

Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다. 업데이트된 후에 Razor 파일을 편집하는 작업은 빌드 시 지원됩니다. 기본적으로 Razor 파일을 컴파일하는 데 필요한 컴파일된 *Views.dll*(*.cshtml* 파일 없음) 또는 참조 어셈블리만 앱과 함께 배포됩니다.

> [!IMPORTANT]
> 사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다. [Razor Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.
>
> Razor SDK는 미리 컴파일 특정 속성이 프로젝트 파일에서 설정되지 않은 경우에만 유효합니다. 예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성됩니다.

## <a name="runtime-compilation"></a>런타임 컴파일

빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다. *.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end