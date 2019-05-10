---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: 부분 뷰를 사용 하 여 ASP.NET Core에서 클래스 라이브러리에 다시 사용할 수 있는 Razor UI를 만드는 방법에 설명 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/07/2018
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 7ec36cc8f4832fb1e1a50831dfcb88f3cafb5ca9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895290"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용 하 여 다시 사용할 수 있는 UI 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

RCL(Razor 클래스 라이브러리)에 Razor 보기, 페이지, 컨트롤러, 페이지 모델, [보기 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 빌드할 수 있습니다. RCL은 패키지되고 재사용될 수 있습니다. 애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다. 보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그(*.cshtml* 파일)가 우선적으로 적용됩니다.

이 기능을 사용 [!INCLUDE[](~/includes/2.1-SDK.md)]

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI를 포함하는 클래스 라이브러리 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.
* 라이브러리 이름 지정(예: "RazorClassLib") > **확인**입니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인**을 선택합니다.

Razor 클래스 라이브러리에는 다음 프로젝트 파일에 있습니다.

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 `dotnet new razorclasslib`을 실행합니다. 예를 들어 다음과 같습니다.

```console
dotnet new razorclasslib -o RazorUIClassLib
```

자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.

---

RCL에 Razor 파일을 추가합니다.

ASP.NET Core 템플릿은 가정 RCL 콘텐츠를 *영역* 폴더입니다. 참조 [RCL 페이지 레이아웃](#afs) 노출 하는 RCL 콘텐츠를 만들려는 `~/Pages` 대신 `~/Areas/Pages`합니다.

## <a name="referencing-razor-class-library-content"></a>Razor 클래스 라이브러리 콘텐츠 참조

RCL은 다음에서 참조할 수 있습니다.

* NuGet 패키지. [NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.
* *{ProjectName}.csproj*. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a>연습: Razor 클래스 라이브러리 프로젝트를 만들고 사용 하 여 Razor 페이지 프로젝트에서

[전체 프로젝트](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다. 샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다. 샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.

### <a name="test-the-download-app"></a>다운로드 앱 테스트

완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-a-razor-class-library)으로 건너 뜁니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 *.sln* 파일을 엽니다. 앱을 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.

```console
dotnet build
```

*WebApp1* 디렉터리로 이동해 앱을 실행합니다.

```console
dotnet run
```

---

[WebApp1 테스트](#test)의 지침 준수

## <a name="create-a-razor-class-library"></a>Razor 클래스 라이브러리 만들기

이 섹션에서는 Razor 클래스 라이브러리(RCL)를 만듭니다. Razor 파일이 RCL에 추가됩니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL 프로젝트를 만듭니다.

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.
* 앱의 이름을 **RazorUIClassLib** > **확인**합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인**을 선택합니다.
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*이라는 Razor 부분 보기 파일을 추가합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 다음을 실행하세요.

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

이전 명령은:

* `RazorUIClassLib` Razor 클래스 라이브러리(RCL)를 만듭니다.
* Razor _Message 페이지를 만들어 RCL에 추가합니다. `-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.
* 만듭니다는 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일과 RCL에 추가 합니다.

합니다 *_ViewStart.cshtml* 파일 (다음 섹션에 추가 됩니다)이 표시 되는 Razor 페이지 프로젝트의 레이아웃을 사용 해야 합니다.

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor 파일 및 폴더를 프로젝트에 추가

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*에서 태그를 다음 코드로 바꿉니다.

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml*에서 태그를 다음 코드로 바꿉니다.

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다. `@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다. 예를 들어 다음과 같습니다.

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

에 대 한 자세한 *_ViewImports.cshtml*를 참조 하세요 [공유 된 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)

* 컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.

```console
dotnet build RazorUIClassLib
```

빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll*을 포함합니다. *RazorUIClassLib.Views.dll*은 컴파일된 Razor 콘텐츠를 포함합니다.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor 페이지 프로젝트에서 Razor UI 라이브러리 사용

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor 페이지 웹앱을 만듭니다.

* **솔루션 탐색기**에서 솔루션 > **추가** >  **새 프로젝트**를 마우스 오른쪽 단추로 클릭합니다.
* **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.
* **WebApp1** 앱 이름을 지정합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **웹 응용 프로그램** > **확인**을 선택합니다.

* **솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정**을 선택합니다.
* **솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **빌드 종속성** > **프로젝트 종속성**을 선택합니다.
* **RazorUIClassLib**를 **WebApp1**의 종속성으로 확인합니다.
* **솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **추가** > **참조**를 선택합니다.
* **참조 관리자** 대화 상자에서 **RazorUIClassLib** > **확인**을 확인합니다.

앱을 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor 페이지 앱 및 Razor 클래스 라이브러리를 포함하는 솔루션 파일 및 Razor 페이지 웹앱을 만듭니다.

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

웹앱을 빌드하고 실행합니다.

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>WebApp1 테스트

Razor UI 클래스 라이브러리를 사용하고 있는지 확인합니다.

* `/MyFeature/Page1`으로 이동합니다.

## <a name="override-views-partial-views-and-pages"></a>보기, 부분 보기 및 페이지 재정의

보기, 부분 보기 또는 Razor 페이지가 웹앱 및 Razor 클래스 라이브러리 모두에 있는 경우 웹앱에서 Razor 태그(*.cshtml* 파일)가 우선적으로 적용됩니다. 예를 들어, 추가 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* WebApp1, 하는 구성이 WebApp1의 Page1 Razor 클래스 라이브러리의 Page1 우선적으로 적용 됩니다 및 합니다.

샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다. 새 위치를 나타내기 위해 태그를 업데이트합니다. 해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL 페이지 레이아웃

RCL 콘텐츠 웹 앱의 일부인 것 처럼 참조 *페이지* 폴더를 다음 파일 구조로 RCL 프로젝트를 만듭니다.

* *RazorUIClassLib/페이지*
* *RazorUIClassLib/페이지/공유*

가정 *RazorUIClassLib/페이지/Shared* 부분 파일이 두: *_Header.cshtml* 하 고 *_Footer.cshtml*합니다. 합니다 `<partial>` 태그를 추가할 수 없습니다 *_Layout.cshtml* 파일:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```
