---
title: ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙
author: guardrex
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 4e07b5803adbce94982584212fa65afbfd427b64
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893510"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙

[Luke Latham](https://github.com/guardrex)으로

페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.

개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.

페이지 경로 지정, 경로 세그먼트를 추가 하거나 매개 변수는 경로를 추가 하려면 페이지의 사용 `@page` 지시문입니다. 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)합니다.

경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어 있습니다. 자세한 내용은 참조 하세요. [라우팅: 예약 된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

| 시나리오 | 샘플에서는 다음 사항을 보여줍니다. |
| -------- | --------------------------- |
| [모델 규칙](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 경로 템플릿 및 헤더를 앱의 페이지에 추가합니다. |
| [페이지 경로 작업 규칙](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다. |
| [페이지 모델 작업 규칙](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</li></ul> | 폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다. |

Razor 페이지 규칙을 추가 하 고 사용 하 여 구성 합니다 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 서비스 컬렉션에는 `Startup` 클래스입니다. 다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>경로 순서

경로 지정을 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (경로 일치)을 처리 합니다.

| 순서            | 동작 |
| :--------------: | -------- |
| -1               | 경로 다른 경로 처리 되기 전에 처리 됩니다. |
| 0                | 순서 지정 하지 않으면 (기본값). 할당 되지 `Order` (`Order = null`) 경로 기본값 `Order` 처리를 위해 0 (영)입니다. |
| 1, 2, &hellip; n | 경로 처리 순서를 지정합니다. |

경로 처리 규칙에 따라 설정 됩니다.

* 경로 순서 대로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).
* 경로 있는 경우 동일한 `Order`가장 덜 구체적인 경로 뒤에 먼저 특정 경로가 일치 합니다.
* 때 동일한 경로 `Order` 매개 변수 수가 같은 일치 요청 URL을 경로에 추가 된 순서 대로 처리 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>합니다.

가능 하면 설정 된 경로 처리 순서에 따라 하지 마세요. 일반적으로 라우팅 URL 일치 하는 올바른 경로 선택합니다. 경로 설정 해야 하는 경우 `Order` 아마도 클라이언트로 복잡 하 고 유지 하기 위해 취약 한 응용 프로그램의 라우팅 체계는 속성 경로를 올바르게 요청 합니다. 앱의 라우팅 체계를 간소화 하기 위해 검색 합니다. 샘플 앱은 단일 앱을 사용 하는 여러 라우팅 시나리오를 시연 하려면 처리 명시적인 경로 필요 합니다. 그러나 설정 경로의 연습을 방지 하려고 해야 `Order` 프로덕션 앱에서.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. MVC 항목의 경로 순서에 대 한 정보는 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)합니다.

## <a name="model-conventions"></a>모델 규칙

에 대 한 대리자를 추가 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 추가할 [규칙 모델](xref:mvc/controllers/application-model#conventions) Razor 페이지에 적용 되는 합니다.

### <a name="add-a-route-model-convention-to-all-pages"></a>모든 페이지에 경로 모델 규칙 추가

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 페이지 경로 중 적용 되는 인스턴스 모델 생성 합니다.

샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다. 이렇게 하면 샘플 앱의 동작을 일치 하는 다음 경로:

* 에 대 한 경로 템플릿을 `TheContactPage/{text?}` 항목의 뒷부분에 나오는 추가 됩니다. 연락처 페이지 경로는 기본 순서 `null` (`Order = 0`) 하기 전에 일치 하도록는 `{globalTemplate?}` 경로 템플릿입니다.
* `{aboutTemplate?}` 항목의 뒷부분에 나오는 경로 템플릿이 추가 됩니다. `{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. `/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.
* `{otherPagesTemplate?}` 항목의 뒷부분에 나오는 경로 템플릿이 추가 됩니다. `{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. 모든 페이지는 *페이지/OtherPages* 폴더 경로 매개 변수를 사용 하 여 요청 (예를 들어 `/OtherPages/Page1/RouteDataValue`), "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.

가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다. 올바른 경로 선택 하려면 라우팅에 의존 합니다.

Razor 페이지 옵션을 추가 하는 등 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, MVC 서비스 컬렉션에 추가 되 면 추가 `Startup.ConfigureServices`합니다. 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>모든 페이지에 앱 모델 규칙 추가

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중 적용 되는 인스턴스 페이지 앱 모델 생성 합니다.

이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다. 클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다. 이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다. 전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.

샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>모든 페이지에는 처리기 모델 규칙 추가

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중 적용 되는 인스턴스 페이지 처리기 모델 생성 합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>페이지 경로 작업 규칙

파생 되는 기본 경로 모델 공급자 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 페이지 경로 구성 하는 것에 대 한 확장성 지점을 제공 하도록 설계 된 규칙을 호출 합니다.

### <a name="folder-route-model-convention"></a>폴더 경로 모델 규칙

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 모든 지정 된 폴더 아래에 있는 페이지에 대 한 합니다.

샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 이렇게 하면에 대 한 템플릿을 `{globalTemplate?}` (항목의 앞부분에서 설정 `1`) 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 지정 됩니다. 경우 페이지에는 *페이지/OtherPages* 폴더 경로 매개 변수 값을 사용 하 여 요청 (예를 들어 `/OtherPages/Page1/RouteDataValue`), "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.

가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다. 올바른 경로 선택 하려면 라우팅에 의존 합니다.

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>페이지 경로 모델 규칙

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 지정한 이름 가진 페이지에 대 한 합니다.

샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 이렇게 하면에 대 한 템플릿을 `{globalTemplate?}` (항목의 앞부분에서 설정 `1`) 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 지정 됩니다. 경로 매개 변수 값에서 정보 페이지를 요청 하는 경우 `/About/RouteDataValue`, "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["aboutTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.

가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다. 올바른 경로 선택 하려면 라우팅에 의존 합니다.

`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정

ASP.NET Core에서 생성 된 페이지 경로 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하고 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

`PageRouteTransformerConvention` 페이지 경로 모델 규칙 폴더 및 파일 이름 부분은 앱에서 자동으로 생성 된 페이지 경로 매개 변수 변환기를 적용 합니다. 예를 들어, Razor 페이지 파일을 */Pages/SubscriptionManagement/ViewAll.cshtml* 해당 경로에서 다시 작성 해야 `/SubscriptionManagement/ViewAll` 하려면 `/subscription-management/view-all`합니다.

`PageRouteTransformerConvention` Razor 페이지 폴더 및 파일 이름에서 제공 되는 자동으로 생성 된 페이지 경로 세그먼트를만 변환 합니다. 사용 하 여 추가 경로 세그먼트를 변환 하지 않습니다는 `@page` 지시문입니다. 규칙 또한 변형 하지 않습니다 하 여 추가 된 경로 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>합니다.

합니다 `PageRouteTransformerConvention` 에 옵션으로 등록 되어 `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
                        new SlugifyParameterTransformer()));
            });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a>페이지 경로 구성

사용 하 여 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 지정된 페이지 경로에 페이지에 대 한 경로 구성 합니다. 페이지에 생성된 링크는 지정된 경로를 사용합니다. `AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.

샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.

연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다. 방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요. 추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다. 렌더링된 페이지에서는 'text' 세그먼트 값을 보여줍니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>페이지 모델 작업 규칙

구현 하는 기본 페이지 모델 공급자 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 페이지 모델을 구성 하는 것에 대 한 확장성 지점을 제공 하도록 설계 된 규칙을 호출 합니다. 이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.

샘플 앱에서는이 섹션의 예는 `AddHeaderAttribute` 클래스는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, 응답 헤더를 적용 하는:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.

**폴더 앱 모델 규칙**

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 지정된 된 폴더 아래 모든 페이지에 대 한 인스턴스.

이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

**페이지 앱 모델 규칙**

사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 지정한 이름 가진 페이지에 대 한 합니다.

샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

**필터 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정된 된 필터를 적용을 구성 합니다. 필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다. 조건이 통과하는 경우 헤더가 추가됩니다. 그렇지 않으면 `EmptyFilter`이 적용됩니다.

`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다. Razor 페이지에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`이 작동되지 않습니다.

`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

**필터 팩터리 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 적용할 지정 된 팩터리 구성 [필터](xref:mvc/controllers/filters) 모든 Razor 페이지에 있습니다.

샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 필터 및 페이지 필터(IPageFilter)

Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다. 다른 유형의 MVC 필터는 사용할 수 있습니다. [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters)를 [Resource](xref:mvc/controllers/filters#resource-filters), 및 [결과](xref:mvc/controllers/filters#result-filters)합니다. 자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.

페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor 페이지에 적용 되는 필터입니다. 자세한 내용은 [Razor 페이지 필터의 메서드](xref:razor-pages/filter)를 참고하시기 바랍니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
