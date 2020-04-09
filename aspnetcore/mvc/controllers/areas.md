---
title: ASP.NET Core의 영역
author: rick-anderson
description: 관련 기능을 별도의 네임스페이스(라우팅용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 ASP.NET MVC 기능인 영역에 대해 알아봅니다.
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625925"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Core의 영역

[다난제이 쿠마르와](https://twitter.com/debug_mode) [릭 앤더슨](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

영역은 관련 기능을 그룹으로 구성하는 데 사용되는 ASP.NET 기능입니다.

* 라우팅을 위한 네임스페이스입니다.
* 보기 및 Razor 페이지에 대한 폴더 구조입니다.

영역을 사용하면 `controller` 및 `action` 또는 Razor Page `page`에 또 다른 경로 매개 변수인 `area`를 추가하여 라우팅을 위한 계층 구조를 만들 수 있습니다.

영역은 ASP.NET Core 웹앱을 자체적인 Razor Pages, 컨트롤러, 보기 및 모델 집합을 갖는 각각의 작은 기능 그룹으로 분할하는 방법을 제공합니다. 영역은 사실상 앱 내부의 구조입니다. ASP.NET Core 웹 프로젝트에서 페이지, 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관됩니다. ASP.NET Core 런타임은 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다. 대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다. 결제, 청구 및 검색과 같은 여러 비즈니스 단위가 있는 전자상거래 앱을 예로 들 수 있습니다. 이러한 각 단위에는 보기, 컨트롤러, Razor Pages 및 모델을 포함하는 고유의 영역이 있습니다.

다음과 같은 경우 프로젝트에서 영역을 사용하는 것이 좋습니다.

* 앱은 논리적으로 분리할 수 있는 여러 개의 고급 기능 구성 요소로 이루어져 있습니다.
* 각 기능 영역을 독립적으로 작업할 수 있도록 앱을 나누고자 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 샘플은 테스트 영역에 대한 기본 앱을 제공합니다.

Razor Pages를 사용하는 경우 이 문서의 [Razor Pages를 사용한 영역](#areas-with-razor-pages)을 참조하세요.

## <a name="areas-for-controllers-with-views"></a>보기가 있는 컨트롤러 영역

영역, 컨트롤러 및 보기를 사용하는 일반적인 ASP.NET Core 웹앱은 다음을 포함합니다.

* [영역 폴더 구조](#area-folder-structure).
* 컨트롤러를 영역과 연결하는 [`[Area]`](#attribute) 특성이 있는 컨트롤러:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [시작에 추가된 영역 경로](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>영역 폴더 구조

*Products* 및 *Services*의 두 논리 그룹이 존재하는 앱을 고려해보세요. 영역을 사용할 경우 폴더 구조는 다음과 유사할 것입니다.

* 프로젝트 이름
  * Areas
    * Products
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * 보기
        * 홈
          * Index.cshtml
        * 관리
          * Index.cshtml
          * About.cshtml
    * Services
      * Controllers
        * HomeController.cs
      * 보기
        * 홈
          * Index.cshtml

영역을 사용할 때는 이전 레이아웃이 일반적인 반면, 이 폴더 구조를 사용하기 위해서는 보기 파일만 필요합니다. 보기 검색은 일치하는 영역 보기 파일을 다음 순서로 검색합니다.

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>컨트롤러를 영역과 연결

영역 제어기는 [ &lbrack;영역&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성으로 지정됩니다.

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>영역 경로 추가

지역 경로는 일반적으로 [특성](xref:mvc/controllers/routing#ar)라우팅 대신 [기존 라우팅을](xref:mvc/controllers/routing#cr) 사용합니다. 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.

모든 영역에서 url 공간이 동일한 경우 `{area:...}`를 경로 템플릿의 토큰으로 사용할 수 있습니다.

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

이전 코드에서 `exists`는 경로가 영역과 일치해야 한다는 제약 조건을 적용합니다. `{area:...}` 사용 `MapControllerRoute`:

* 영역에 라우팅을 추가하는 가장 복잡한 메커니즘입니다.
* 모든 컨트롤러를 특성과 일치시다. `[Area("Area name")]`

다음 코드는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>를 사용하여 명명된 두 개의 영역 경로를 만듭니다.

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

자세한 내용은 [영역 라우팅](xref:mvc/controllers/routing#areas)을 참조하세요.

### <a name="link-generation-with-mvc-areas"></a>MVC 영역과 링크 생성

[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)의 다음 코드는 지정된 영역과 링크 생성을 보여줍니다.

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

샘플 다운로드에는 다음이 포함된 [부분 보기가](xref:mvc/views/partial) 포함됩니다.

* 앞의 링크입니다.
* 이전과 유사한 `area` 링크는 지정되지 않습니다.

이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다. 영역을 지정하지 않고 생성된 링크는 동일한 영역 및 컨트롤러 페이지에서 참조할 때만 유효합니다.

영역 또는 컨트롤러를 지정하지 않으면 라우팅은 [앰비언트](xref:mvc/controllers/routing#ambient) 값에 따라 달라집니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. 대부분의 경우 샘플 앱의 경우 주변 값을 사용하면 영역을 지정하지 않는 태그가 잘못된 링크가 생성됩니다.

자세한 정보는 [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)을 참조하세요.

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart.cshtml 파일을 사용하여 영역에 대한 레이아웃 공유

전체 앱에 대한 공통 레이아웃을 공유하려면 [응용 프로그램 루트 폴더에](#arf) *_ViewStart.cshtml을* 유지합니다. 자세한 내용은 <xref:mvc/views/layout>를 참조하세요.

<a name="arf"></a>

### <a name="application-root-folder"></a>응용 프로그램 루트 폴더

응용 프로그램 루트 폴더는 ASP.NET 코어 템플릿으로 만든 웹 앱에서 *Startup.cs* 포함하는 폴더입니다.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */보기/_ViewImports.cshtml*, MVC및 Razor 페이지에 대한 */페이지/_ViewImports.cshtml은* 영역의 보기로 가져오지 않습니다. 다음 방법 중 하나를 사용하여 모든 보기에 보기 가져오기를 제공합니다.

* [응용 프로그램 루트 폴더에](#arf) *_ViewImports.cshtml을 추가합니다.* 응용 프로그램 루트 폴더의 *_ViewImports.cshtml은* 앱의 모든 보기에 적용됩니다.
* *_ViewImports.cshtml* 파일을 영역 아래의 적절한 보기 폴더에 복사합니다.

*_ViewImports.cshtml* 파일에는 일반적으로 [태그](xref:mvc/views/tag-helpers/intro) 도우미 `@using`가져오기 `@inject` 및 문이 포함됩니다. 자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하십시오.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>보기가 저장된 기본 영역 폴더 변경

다음 코드는 기본 영역 폴더를 `"Areas"`에서 `"MyAreas"`로 변경합니다.

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Razor Pages가 있는 영역

Razor 페이지가 있는 `Areas/<area name>/Pages` 영역에는 앱 루트에 있는 폴더가 필요합니다. [예제 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)에서는 다음 폴더 구조가 사용됩니다.

* 프로젝트 이름
  * Areas
    * Products
      * 페이지
        * _ViewImports
        * 정보
        * 인덱스
    * Services
      * 페이지
        * 관리
          * 정보
          * 인덱스

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Pages 및 영역과 링크 생성

[예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)의 다음 코드는 영역이 지정된(예: `asp-area="Products"`) 링크 생성을 보여줍니다.

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다. 이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다. 영역을 지정하지 않고 생성된 링크는 동일한 영역의 페이지에서 참조할 때만 유효합니다.

영역을 지정하지 않으면 *앰비언트* 값에 따라 라우팅이 달라집니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. 예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다. 예를 들어 다음 코드에서 생성된 링크를 살펴보겠습니다.

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

이전 코드에서:

* `<a asp-page="/Manage/About">`에서 생성된 링크는 `Services` 영역의 페이지를 마지막으로 요청한 경우에만 유효합니다. `/Services/Manage/`, `/Services/Manage/Index` 또는 `/Services/Manage/About`).
* `<a asp-page="/About">`에서 생성된 링크는 `/Home`의 페이지를 마지막으로 요청한 경우에만 유효합니다.
* 이 코드는 [예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)에서 가져온 것입니다.

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>_ViewImports 파일을 사용하여 네임스페이스 및 태그 도우미 가져오기

각 영역의 *Pages* 폴더에 *_ViewImports.cshtml* 파일을 추가하여 네임스페이스 및 태그 도우미를 폴더의 각 Razor Page로 가져올 수 있습니다.

*_ViewImports.cshtml* 파일이 없는 샘플 코드의 *Services* 영역을 살펴보겠습니다. 다음 태그는 */Services/Manage/About* Razor 페이지를 보여 줍니다.

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

이전 태그에서:

* 모델(`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)을 지정하려면 정규화된 도메인 이름을 사용해야 합니다.
* [태그 도우미는](xref:mvc/views/tag-helpers/intro) 다음을 통해 활성화됩니다.`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

예제 다운로드에서 Products 영역에는 다음과 같은 *_ViewImports.cshtml* 파일이 존재합니다.

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

다음 태그는 */Products/About* Razor 페이지를 보여 줍니다.

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

앞의 파일에서 네임스페이스 `@addTagHelper` 및 지시문은 *영역/제품/페이지/_ViewImports.cshtml* 파일에 의해 파일로 가져옵니다.

자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) 및 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.

### <a name="shared-layout-for-razor-pages-areas"></a>Razor Pages 영역에 대한 공유 레이아웃

전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml*을 애플리케이션 루트 폴더로 이동합니다.

### <a name="publishing-areas"></a>영역 게시

*.csproj 파일에 `<Project Sdk="Microsoft.NET.Sdk.Web">``이 포함되면 *.cshtml 파일 및 *wwwroot* 디렉터리 내의 모든 파일이 출력에 게시됩니다.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

영역은 관련 기능을 별개의 네임스페이스(라우팅용) 및 폴더 구조(보기용)의 그룹으로 구조화하는 데 사용되는 ASP.NET 기능입니다. 영역을 사용하면 `controller` 및 `action` 또는 Razor Page `page`에 또 다른 경로 매개 변수인 `area`를 추가하여 라우팅을 위한 계층 구조를 만들 수 있습니다.

영역은 ASP.NET Core 웹앱을 자체적인 Razor Pages, 컨트롤러, 보기 및 모델 집합을 갖는 각각의 작은 기능 그룹으로 분할하는 방법을 제공합니다. 영역은 사실상 앱 내부의 구조입니다. ASP.NET Core 웹 프로젝트에서 페이지, 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관됩니다. ASP.NET Core 런타임은 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다. 대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다. 결제, 청구 및 검색과 같은 여러 비즈니스 단위가 있는 전자상거래 앱을 예로 들 수 있습니다. 이러한 각 단위에는 보기, 컨트롤러, Razor Pages 및 모델을 포함하는 고유의 영역이 있습니다.

다음과 같은 경우 프로젝트에서 영역을 사용하는 것이 좋습니다.

* 앱은 논리적으로 분리할 수 있는 여러 개의 고급 기능 구성 요소로 이루어져 있습니다.
* 각 기능 영역을 독립적으로 작업할 수 있도록 앱을 나누고자 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 샘플은 테스트 영역에 대한 기본 앱을 제공합니다.

Razor Pages를 사용하는 경우 이 문서의 [Razor Pages를 사용한 영역](#areas-with-razor-pages)을 참조하세요.

## <a name="areas-for-controllers-with-views"></a>보기가 있는 컨트롤러 영역

영역, 컨트롤러 및 보기를 사용하는 일반적인 ASP.NET Core 웹앱은 다음을 포함합니다.

* [영역 폴더 구조](#area-folder-structure).
* 컨트롤러를 영역과 연결하는 [`[Area]`](#attribute) 특성이 있는 컨트롤러:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [시작에 추가된 영역 경로](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>영역 폴더 구조

*Products* 및 *Services*의 두 논리 그룹이 존재하는 앱을 고려해보세요. 영역을 사용할 경우 폴더 구조는 다음과 유사할 것입니다.

* 프로젝트 이름
  * Areas
    * Products
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * 보기
        * 홈
          * Index.cshtml
        * 관리
          * Index.cshtml
          * About.cshtml
    * Services
      * Controllers
        * HomeController.cs
      * 보기
        * 홈
          * Index.cshtml

영역을 사용할 때는 이전 레이아웃이 일반적인 반면, 이 폴더 구조를 사용하기 위해서는 보기 파일만 필요합니다. 보기 검색은 일치하는 영역 보기 파일을 다음 순서로 검색합니다.

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>컨트롤러를 영역과 연결

영역 제어기는 [ &lbrack;영역&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성으로 지정됩니다.

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>영역 경로 추가

영역 경로는 일반적으로 특성 라우팅보다는 규칙 기반 라우팅을 사용합니다. 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.

모든 영역에서 url 공간이 동일한 경우 `{area:...}`를 경로 템플릿의 토큰으로 사용할 수 있습니다.

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

이전 코드에서 `exists`는 경로가 영역과 일치해야 한다는 제약 조건을 적용합니다. `{area:...}`를 사용하는 것은 영역에 라우팅을 추가하는 가장 간단한 메커니즘입니다.

다음 코드는 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>를 사용하여 명명된 두 개의 영역 경로를 만듭니다.

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

ASP.NET Core 2.2에서 `MapAreaRoute`를 사용하는 경우에는 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore/issues/7772)를 참조하세요.

자세한 내용은 [영역 라우팅](xref:mvc/controllers/routing#areas)을 참조하세요.

### <a name="link-generation-with-mvc-areas"></a>MVC 영역과 링크 생성

[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)의 다음 코드는 지정된 영역과 링크 생성을 보여줍니다.

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

이전 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.

예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다. 이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다. 영역을 지정하지 않고 생성된 링크는 동일한 영역 및 컨트롤러 페이지에서 참조할 때만 유효합니다.

영역 또는 컨트롤러를 지정하지 않으면 라우팅은 *앰비언트* 값에 따라 달라집니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. 예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다.

자세한 정보는 [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)을 참조하세요.

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart.cshtml 파일을 사용하여 영역에 대한 레이아웃 공유

전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml*을 애플리케이션 루트 폴더로 이동합니다.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

표준 위치의 */Views/_ViewImports.cshtml*은 영역에 적용되지 않습니다. 일반적인 [태그 도우미를](xref:mvc/views/tag-helpers/intro) `@using`사용하려면 `@inject` 또는 해당 지역에서 적절한 *_ViewImports.cshtml* 파일이 [영역 보기에 적용되었는지 확인합니다.](xref:mvc/views/layout#importing-shared-directives) 모든 보기에서 동일한 동작을 원하는 경우 */Views/_ViewImports.cshtml*을 응용 프로그램 루트로 이동합니다.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>보기가 저장된 기본 영역 폴더 변경

다음 코드는 기본 영역 폴더를 `"Areas"`에서 `"MyAreas"`로 변경합니다.

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Razor Pages가 있는 영역

Razor 페이지가 있는 `Areas/<area name>/Pages` 영역에는 앱 루트에 있는 폴더가 필요합니다. [예제 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)에서는 다음 폴더 구조가 사용됩니다.

* 프로젝트 이름
  * Areas
    * Products
      * 페이지
        * _ViewImports
        * 정보
        * 인덱스
    * Services
      * 페이지
        * 관리
          * 정보
          * 인덱스

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Pages 및 영역과 링크 생성

[예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)의 다음 코드는 영역이 지정된(예: `asp-area="Products"`) 링크 생성을 보여줍니다.

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

이전 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.

예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다. 이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다. 영역을 지정하지 않고 생성된 링크는 동일한 영역의 페이지에서 참조할 때만 유효합니다.

영역을 지정하지 않으면 *앰비언트* 값에 따라 라우팅이 달라집니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. 예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다. 예를 들어 다음 코드에서 생성된 링크를 살펴보겠습니다.

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

이전 코드에서:

* `<a asp-page="/Manage/About">`에서 생성된 링크는 `Services` 영역의 페이지를 마지막으로 요청한 경우에만 유효합니다. `/Services/Manage/`, `/Services/Manage/Index` 또는 `/Services/Manage/About`).
* `<a asp-page="/About">`에서 생성된 링크는 `/Home`의 페이지를 마지막으로 요청한 경우에만 유효합니다.
* 이 코드는 [예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)에서 가져온 것입니다.

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>_ViewImports 파일을 사용하여 네임스페이스 및 태그 도우미 가져오기

각 영역의 *Pages* 폴더에 *_ViewImports.cshtml* 파일을 추가하여 네임스페이스 및 태그 도우미를 폴더의 각 Razor Page로 가져올 수 있습니다.

*_ViewImports.cshtml* 파일이 없는 샘플 코드의 *Services* 영역을 살펴보겠습니다. 다음 태그는 */Services/Manage/About* Razor 페이지를 보여 줍니다.

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

이전 태그에서:

* 모델(`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)을 지정하려면 정규화된 도메인 이름을 사용해야 합니다.
* [태그 도우미는](xref:mvc/views/tag-helpers/intro) 다음을 통해 활성화됩니다.`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

예제 다운로드에서 Products 영역에는 다음과 같은 *_ViewImports.cshtml* 파일이 존재합니다.

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

다음 태그는 */Products/About* Razor 페이지를 보여 줍니다.

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

앞의 파일에서 네임스페이스 `@addTagHelper` 및 지시문은 *영역/제품/페이지/_ViewImports.cshtml* 파일에 의해 파일로 가져옵니다.

자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) 및 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.

### <a name="shared-layout-for-razor-pages-areas"></a>Razor Pages 영역에 대한 공유 레이아웃

전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml*을 애플리케이션 루트 폴더로 이동합니다.

### <a name="publishing-areas"></a>영역 게시

*.csproj 파일에 `<Project Sdk="Microsoft.NET.Sdk.Web">``이 포함되면 *.cshtml 파일 및 *wwwroot* 디렉터리 내의 모든 파일이 출력에 게시됩니다.
::: moniker-end
