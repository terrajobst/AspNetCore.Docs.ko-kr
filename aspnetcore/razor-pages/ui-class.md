---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: 부분 뷰를 사용 하 여 ASP.NET Core에서 클래스 라이브러리에 다시 사용할 수 있는 Razor UI를 만드는 방법에 설명 합니다.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 8813244ea6d00b170d9f95d12743e9fee38bf810
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172655"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용 하 여 다시 사용할 수 있는 UI 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [razor 구성 요소](xref:blazor/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components)및 데이터 모델을 rcl (razor 클래스 라이브러리)로 빌드할 수 있습니다. RCL은 패키지되고 재사용될 수 있습니다. 애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다. 보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI를 포함하는 클래스 라이브러리 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio에서 새 **프로젝트 만들기를**선택 합니다.
* **Razor 클래스 라이브러리** > **다음**을 선택 합니다.
* 라이브러리 이름 (예: "RazorClassLib")을 > 하 여 **만듭니다**. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.
* 보기를 지원 해야 하는 경우 **지원 페이지 및 보기** 를 선택 합니다. 기본적으로 Razor Pages만 지원 됩니다. **만들기**를 선택합니다.

RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다. **지원 페이지 및 보기** 옵션은 페이지 및 보기를 지원 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 `dotnet new razorclasslib`을 실행합니다. 예들 들어 다음과 같습니다.

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다. `--support-pages-and-views` 옵션 (`dotnet new razorclasslib --support-pages-and-views`)을 전달 하 여 페이지 및 보기에 대 한 지원을 제공 합니다.

자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.

---

RCL에 Razor 파일을 추가합니다.

ASP.NET Core 템플릿은 RCL 콘텐츠가 *영역* 폴더에 있다고 가정 합니다. `~/Areas/Pages`대신 `~/Pages`에서 콘텐츠를 노출 하는 RCL을 만들려면 [Rcl 페이지 레이아웃](#rcl-pages-layout) 을 참조 하세요.

## <a name="reference-rcl-content"></a>RCL 콘텐츠 참조

RCL은 다음에서 참조할 수 있습니다.

* NuGet 패키지. [NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.
* *{ProjectName}.csproj*. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.

## <a name="override-views-partial-views-and-pages"></a>보기, 부분 보기 및 페이지 재정의

보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다. 예를 들어 *WebApp1/Areas/MyFeature/Pages/* WebApp1에 추가 하 고 WebApp1의는 rcl의 페이지 1 보다 우선 합니다.

샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다. 새 위치를 나타내기 위해 태그를 업데이트합니다. 해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.

### <a name="rcl-pages-layout"></a>RCL 페이지 레이아웃

RCL 콘텐츠를 웹 앱의 *Pages* 폴더에 포함 된 것 처럼 참조 하려면 다음 파일 구조를 사용 하 여 rcl 프로젝트를 만듭니다.

* *RazorUIClassLib/페이지*
* *RazorUIClassLib/Pages/Shared*

*RazorUIClassLib/Pages/Shared* 에 두 개의 부분 파일이 포함 되어 있다고 가정 합니다. *_Header. cshtml* 및 *_Footer*. `<partial>` 태그를 *_Layout. cshtml* 파일에 추가할 수 있습니다.

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>정적 자산을 사용 하 여 RCL 만들기

Rcl에는 rcl 또는 RCL의 소비 앱에서 참조할 수 있는 도우미 정적 자산이 필요할 수 있습니다. ASP.NET Core를 사용 하면 소비 하는 앱에서 사용할 수 있는 정적 자산이 포함 된 RCLs를 만들 수 있습니다.

RCL의 일부로 동반 자산을 포함 하려면 클래스 라이브러리에 *wwwroot* 폴더를 만들고 해당 폴더에 필요한 파일을 포함 합니다.

RCL을 압축 하면 *wwwroot* 폴더의 모든 관련 자산이 패키지에 자동으로 포함 됩니다.

### <a name="exclude-static-assets"></a>정적 자산 제외

정적 자산을 제외 하려면 프로젝트 파일의 `$(DefaultItemExcludes)` 속성 그룹에 원하는 제외 경로를 추가 합니다. 항목을 세미콜론 (`;`)으로 구분 합니다.

다음 예제에서 *wwwroot* 폴더의 *lib .css* 스타일 시트는 정적 자산으로 간주 되지 않으며 게시 된 rcl에 포함 되지 않습니다.

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript 통합

RCL에 TypeScript 파일을 포함 하려면 다음을 수행 합니다.

1. TypeScript *폴더 외부에 TypeScript* 파일 (.ts *)을 저장 합니다.* 예를 들어 *클라이언트* 폴더에 파일을 저장 합니다.

1. *Wwwroot* 폴더에 대 한 TypeScript 빌드 출력을 구성 합니다. 프로젝트 파일에서 `PropertyGroup` 내부에 `TypescriptOutDir` 속성을 설정 합니다.

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. 프로젝트 파일의 `PropertyGroup` 내부에 다음 대상을 추가 하 여 TypeScript 대상을 `ResolveCurrentProjectStaticWebAssets` 대상의 종속성으로 포함 합니다.

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>참조 된 RCL에서 콘텐츠 사용

RCL의 *wwwroot* 폴더에 포함 된 파일은 `_content/{LIBRARY NAME}/`접두사의 rcl 또는 소비 앱에 노출 됩니다. 예를 들어, *Razor.* t c m. c a t. c a t. c a t. `_content/Razor.Class.Lib/`c a t. NuGet 패키지를 생성할 때 어셈블리 이름이 패키지 ID와 다른 경우 `{LIBRARY NAME}`의 패키지 ID를 사용 합니다.

소비 앱은 `<script>`, `<style>`, `<img>`및 기타 HTML 태그를 사용 하 여 라이브러리에서 제공 하는 정적 자산을 참조 합니다. 소비 하는 앱은 `Startup.Configure`에서 [정적 파일 지원을](xref:fundamentals/static-files) 사용 하도록 설정 해야 합니다.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

빌드 출력 (`dotnet run`)에서 사용 하는 앱을 실행 하는 경우 개발 환경에서 정적 웹 자산이 기본적으로 사용 됩니다. 빌드 출력에서 실행 될 때 다른 환경에서 자산을 지원 하려면 *Program.cs*의 호스트 작성기에서 `UseStaticWebAssets`를 호출 합니다.

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

게시 된 출력 (`dotnet publish`)에서 앱을 실행 하는 경우 `UseStaticWebAssets`를 호출할 필요가 없습니다.

### <a name="multi-project-development-flow"></a>다중 프로젝트 개발 흐름

소비 앱이 실행 되는 경우:

* RCL의 자산은 원래 폴더에 유지 됩니다. 자산이 소비 앱으로 이동 하지 않습니다.
* Rcl의 *wwwroot* 폴더 내에서 변경 되는 내용은 rcl이 다시 작성 된 후 소비 앱에 반영 되며 소비 하는 앱을 다시 빌드하지 않습니다.

RCL을 빌드할 때 정적 웹 자산 위치를 설명 하는 매니페스트가 생성 됩니다. 소비 앱은 런타임에 매니페스트를 읽어 참조 된 프로젝트 및 패키지의 자산을 사용 합니다. 새 자산이 RCL에 추가 되 면 사용 하는 앱이 새 자산에 액세스할 수 있으려면 먼저 RCL을 다시 작성 하 여 매니페스트를 업데이트 해야 합니다.

### <a name="publish"></a>게시

앱이 게시 되 면 모든 참조 된 프로젝트 및 패키지의 동반 자산이 `_content/{LIBRARY NAME}/`아래 게시 된 앱의 *wwwroot* 폴더로 복사 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [razor 구성 요소](xref:blazor/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components)및 데이터 모델을 rcl (razor 클래스 라이브러리)로 빌드할 수 있습니다. RCL은 패키지되고 재사용될 수 있습니다. 애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다. 보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI를 포함하는 클래스 라이브러리 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션**을 선택합니다.
* 라이브러리 이름 지정(예: "RazorClassLib") > **확인**입니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인을**선택 합니다.

RCL에는 다음과 같은 프로젝트 파일이 있습니다.

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 `dotnet new razorclasslib`을 실행합니다. 예들 들어 다음과 같습니다.

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.

---

RCL에 Razor 파일을 추가합니다.

ASP.NET Core 템플릿은 RCL 콘텐츠가 *영역* 폴더에 있다고 가정 합니다. `~/Areas/Pages`대신 `~/Pages`에서 콘텐츠를 노출 하는 RCL을 만들려면 [Rcl 페이지 레이아웃](#rcl-pages-layout) 을 참조 하세요.

## <a name="reference-rcl-content"></a>RCL 콘텐츠 참조

RCL은 다음에서 참조할 수 있습니다.

* NuGet 패키지. [NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.
* *{ProjectName}.csproj*. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>연습: RCL 프로젝트 만들기 및 Razor Pages 프로젝트에서 사용

[전체 프로젝트](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다. 샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다. 샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.

### <a name="test-the-download-app"></a>다운로드 앱 테스트

완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-an-rcl)으로 건너 뜁니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 *.sln* 파일을 엽니다. 앱을 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.

```dotnetcli
dotnet build
```

*WebApp1* 디렉터리로 이동해 앱을 실행합니다.

```dotnetcli
dotnet run
```

---

[WebApp1 테스트](#test-webapp1)의 지침 준수

## <a name="create-an-rcl"></a>RCL 만들기

이 섹션에서는 RCL을 만듭니다. Razor 파일이 RCL에 추가됩니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL 프로젝트를 만듭니다.

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션**을 선택합니다.
* 앱 이름을 **RazorUIClassLib** > **확인**합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인을**선택 합니다.
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*이라는 Razor 부분 보기 파일을 추가합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 다음을 실행하세요.

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

이전 명령은

* `RazorUIClassLib` RCL을 만듭니다.
* Razor _Message 페이지를 만들어 RCL에 추가합니다. `-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.
* [_ViewStart cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일을 만들어 rcl에 추가 합니다.

*_ViewStart* 파일은 Razor Pages 프로젝트의 레이아웃 (다음 섹션에 추가 됨)을 사용 하는 데 필요 합니다.

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor 파일 및 폴더를 프로젝트에 추가

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*에서 태그를 다음 코드로 바꿉니다.

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml*에서 태그를 다음 코드로 바꿉니다.

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다. `@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다. 예들 들어 다음과 같습니다.

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  _ViewImports에 대 한 자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives) 를 참조 하세요 *.*

* 컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll*을 포함합니다. *RazorUIClassLib.Views.dll*은 컴파일된 Razor 콘텐츠를 포함합니다.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor 페이지 프로젝트에서 Razor UI 라이브러리 사용

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor 페이지 웹앱을 만듭니다.

* **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭 하 > >**새 프로젝트**를 **추가** 합니다.
* **새 ASP.NET Core 웹 애플리케이션**을 선택합니다.
* **WebApp1** 앱 이름을 지정합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **웹 응용 프로그램** > **확인을**선택 합니다.

* **솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정**을 선택합니다.
* **솔루션 탐색기**에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭 하 고 **빌드 종속성** > **프로젝트 종속성**을 선택 합니다.
* **RazorUIClassLib**를 **WebApp1**의 종속성으로 확인합니다.
* **솔루션 탐색기**에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭 하 고 > **참조** **추가** 를 선택 합니다.
* **참조 관리자** 대화 상자에서 **RazorUIClassLib** > **OK**를 선택 합니다.

앱을 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor Pages 앱 및 RCL을 포함 하는 Razor Pages 웹 앱 및 솔루션 파일을 만듭니다.

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

웹앱을 빌드하고 실행합니다.

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>WebApp1 테스트

`/MyFeature/Page1`로 이동 하 여 Razor UI 클래스 라이브러리가 사용 중인지 확인 합니다.

## <a name="override-views-partial-views-and-pages"></a>보기, 부분 보기 및 페이지 재정의

보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다. 예를 들어 *WebApp1/Areas/MyFeature/Pages/* WebApp1에 추가 하 고 WebApp1의는 rcl의 페이지 1 보다 우선 합니다.

샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다. 새 위치를 나타내기 위해 태그를 업데이트합니다. 해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.

### <a name="rcl-pages-layout"></a>RCL 페이지 레이아웃

RCL 콘텐츠를 웹 앱의 *Pages* 폴더에 포함 된 것 처럼 참조 하려면 다음 파일 구조를 사용 하 여 rcl 프로젝트를 만듭니다.

* *RazorUIClassLib/페이지*
* *RazorUIClassLib/Pages/Shared*

*RazorUIClassLib/Pages/Shared* 에 두 개의 부분 파일이 포함 되어 있다고 가정 합니다. *_Header. cshtml* 및 *_Footer*. `<partial>` 태그를 *_Layout. cshtml* 파일에 추가할 수 있습니다.

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/class-libraries>
