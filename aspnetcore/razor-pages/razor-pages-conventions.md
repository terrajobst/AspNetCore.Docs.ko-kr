---
title: ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙
author: guardrex
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914970"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙

[Luke Latham](https://github.com/guardrex)으로

페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.

개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.

페이지 경로를 지정 하거나, 경로 세그먼트를 추가 하거나, 경로에 매개 변수를 추가 하려면 페이지의 `@page` 지시어를 사용 합니다. 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조 하세요.

경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다. 자세한 내용은 라우팅을 참조 [하세요. 예약 된 라우팅](xref:fundamentals/routing#reserved-routing-names)이름

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

| 시나리오 | 샘플에서는 다음 사항을 보여줍니다. |
| -------- | --------------------------- |
| [모델 규칙](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 경로 템플릿 및 헤더를 앱의 페이지에 추가합니다. |
| [페이지 경로 작업 규칙](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다. |
| [페이지 모델 작업 규칙](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</li></ul> | 폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다. |

클래스의 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 서비스컬렉션에대한확장메서드를사용하여RazorPages규칙을추가하고구성합니다.`Startup` 다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention(
                    "/About", model => { ... });
                options.Conventions.AddPageRoute(
                    "/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention(
                    "/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>경로 순서

경로는 처리 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 를 위해를 지정 합니다 (경로 일치).

| 주문            | 동작 |
| :--------------: | -------- |
| -1               | 경로는 다른 경로가 처리 되기 전에 처리 됩니다. |
| 0                | 순서가 지정 되지 않은 경우 (기본값) 할당 `Order` 안 함`Order = null`()은 처리 `Order` 를 위해 경로를 0 (영)으로 설정 합니다. |
| 1, 2, &hellip; n | 경로 처리 순서를 지정 합니다. |

경로 처리는 규칙에 의해 설정 됩니다.

* 경로는 순차적으로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).
* 경로가 동일한 `Order`경우 가장 구체적인 경로는 먼저 일치 하 고 그 다음에 더 작은 경로를 찾습니다.
* 동일 `Order` 하 고 동일한 수의 매개 변수를 포함 하는 경로가 요청 URL과 일치 하는 경우 경로는에 추가 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>된 순서 대로 처리 됩니다.

가능 하면 설정 된 경로 처리 순서에 따라 피하십시오. 일반적으로 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다. 경로 `Order` 속성을 경로 요청을 올바르게 설정 해야 하는 경우에는 응용 프로그램의 라우팅 체계가 클라이언트에 혼란 스 러 울 수 있으며 유지 관리가 취약 합니다. 응용 프로그램의 라우팅 체계를 간소화 하기 위해 검색 합니다. 샘플 앱에는 단일 앱을 사용 하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요 합니다. 그러나 프로덕션 앱에서 경로 `Order` 를 설정 하는 방법은 사용 하지 않는 것이 좋습니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. MVC 항목의 경로 순서에 대 한 정보는 컨트롤러 [작업에 대 한 라우팅에서 사용할 수 있습니다. 특성 경로](xref:mvc/controllers/routing#ordering-attribute-routes)순서 지정.

## <a name="model-conventions"></a>모델 규칙

에 대 한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 대리자를 추가 하 여 Razor Pages에 적용 되는 [모델 규칙](xref:mvc/controllers/application-model#conventions) 을 추가 합니다.

### <a name="add-a-route-model-convention-to-all-pages"></a>모든 페이지에 경로 모델 규칙 추가

를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 페이지 경로 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스의 컬렉션에 추가 합니다.

샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다. 이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작을 수행 합니다.

* 의 `TheContactPage/{text?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다. 연락처 페이지 경로는 ( `null` `Order = 0`)의 기본 순서 `{globalTemplate?}` 이므로 경로 템플릿 이전에 일치 합니다.
* `{aboutTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다. `{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. `/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.
* `{otherPagesTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다. `{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. 경로 매개 변수 (예: `/OtherPages/Page1/RouteDataValue`)를 사용 하 여 *Pages/otherpages* 폴더에 있는 페이지를 요청 하면 "RouteDataValue `RouteData.Values["globalTemplate"]` "는`Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` 속성.

가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요. 경로를 사용 하 여 올바른 경로를 선택 합니다.

추가 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>와 같은 Razor Pages 옵션은 MVC가의 `Startup.ConfigureServices`서비스 컬렉션에 추가 될 때 추가 됩니다. 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>모든 페이지에 앱 모델 규칙 추가

를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 페이지 앱 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스 컬렉션에 추가 합니다.

이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다. 클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다. 이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다. 전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.

샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>모든 페이지에 처리기 모델 규칙 추가

를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 페이지 처리기 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스 컬렉션에 추가 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a>페이지 경로 작업 규칙

에서 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 파생 되는 기본 경로 모델 공급자는 페이지 경로를 구성 하기 위한 확장성 지점도 제공 하도록 설계 된 규칙을 호출 합니다.

### <a name="folder-route-model-convention"></a>폴더 경로 모델 규칙

를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 폴더의 모든 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 에 대해에서 작업을 호출 하는을 만들고 추가 합니다.

샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 이렇게 하면 단일 경로 값을 `{globalTemplate?}` 제공 하는 경우 첫 번째 경로 `1`데이터 값 위치에 대 한 (항목의 앞부분에 설정)에 대 한 템플릿이 우선 순위를 갖습니다. 경로 매개 변수 값 (예: `/OtherPages/Page1/RouteDataValue`)을 사용 하 여 *Pages/otherpages* 폴더의 페이지를 요청 하면 "RouteDataValue `RouteData.Values["globalTemplate"]` "는`Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` 속성.

가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요. 경로를 사용 하 여 올바른 경로를 선택 합니다.

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>페이지 경로 모델 규칙

를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 이름의 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 에 대 한 작업을 호출 하는을 만들고 추가 합니다.

샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 이렇게 하면 단일 경로 값을 `{globalTemplate?}` 제공 하는 경우 첫 번째 경로 `1`데이터 값 위치에 대 한 (항목의 앞부분에 설정)에 대 한 템플릿이 우선 순위를 갖습니다. 에 경로 매개 변수 `/About/RouteDataValue`값을 사용 하 여 About 페이지를 요청 하면 `Order` 속성을 설정 하 여 `RouteData.Values["globalTemplate"]` "`Order = 1`RouteDataValue"가 `RouteData.Values["aboutTemplate"]` `Order = 2`()로 로드 되 고가 아닌 ()으로 로드 됩니다.

가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요. 경로를 사용 하 여 올바른 경로를 선택 합니다.

`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정

ASP.NET Core에서 생성 된 페이지 경로는 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하고 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

페이지 `PageRouteTransformerConvention` 경로 모델 규칙은 응용 프로그램에서 자동으로 생성 된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용 합니다. 예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 의 Razor Pages 파일은에서 `/SubscriptionManagement/ViewAll` 로 `/subscription-management/view-all`다시 작성 된 경로를 갖습니다.

`PageRouteTransformerConvention`는 Razor Pages 폴더 및 파일 이름에서 가져온 페이지 경로의 자동으로 생성 된 세그먼트만 변환 합니다. `@page` 지시문을 사용 하 여 추가 된 경로 세그먼트는 변환 되지 않습니다. 또한 규칙은에 의해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>추가 된 경로를 변환 하지 않습니다.

는 `PageRouteTransformerConvention` 의`Startup.ConfigureServices`옵션으로 등록 됩니다.

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

지정 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 된 페이지 경로에서 페이지에 대 한 경로를 구성 하는 데 사용 합니다. 페이지에 생성된 링크는 지정된 경로를 사용합니다. `AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.

샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.

연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다. 방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요. 추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다. 렌더링된 페이지에서는 'text' 세그먼트 값을 보여줍니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>페이지 모델 작업 규칙

을 구현 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 하는 기본 페이지 모델 공급자는 페이지 모델을 구성 하는 데 필요한 확장성을 제공 하도록 설계 된 규칙을 호출 합니다. 이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.

이 단원의 예제에서 샘플 앱은 응답 헤더를 적용 하 `AddHeaderAttribute` 는 클래스 ( <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>)를 사용 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.

**폴더 앱 모델 규칙**

를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 대 한 인스턴스에 대 한 작업을 호출 하는을 만들고 추가 합니다.

이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

**페이지 앱 모델 규칙**

를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 이름의 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 에 대 한 작업을 호출 하는을 만들고 추가 합니다.

샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

**필터 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>지정 된 필터가 적용 되도록 구성 합니다. 필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다. 조건이 통과하는 경우 헤더가 추가됩니다. 그렇지 않으면 `EmptyFilter`이 적용됩니다.

`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다. 작업 필터는 Razor Pages에서 무시 되므로 경로에 `EmptyFilter` 가 포함 되어 `OtherPages/Page2`있지 않은 경우는 의도 한 대로 적용 되지 않습니다.

`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

**필터 팩터리 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>모든 Razor Pages에 [필터](xref:mvc/controllers/filters) 를 적용 하도록 지정 된 팩터리를 구성 합니다.

샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

*AddHeaderWithFactory.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 필터 및 페이지 필터(IPageFilter)

Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다. 사용할 수 있는 다른 유형의 MVC 필터는 다음과 같습니다. [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters)및 [결과가](xref:mvc/controllers/filters#result-filters)있습니다. 자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.

페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용 되는 필터입니다. 자세한 내용은 [Razor 페이지 필터의 메서드](xref:razor-pages/filter)를 참고하시기 바랍니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
