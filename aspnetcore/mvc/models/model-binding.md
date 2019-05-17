---
title: ASP.NET Core의 모델 바인딩
author: tdykstra
description: ASP.NET Core MVC의 모델 바인딩이 HTTP 요청의 데이터를 작업 메서드 매개 변수에 매핑하는 방법을 알아봅니다.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883148"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core의 모델 바인딩

작성자: [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>모델 바인딩 소개

ASP.NET Core MVC에서 모델 바인딩은 작업 메서드 매개 변수에 HTTP 요청의 데이터를 자동으로 매핑합니다. 매개 변수는 문자열, 정수, float 등과 같은 단순 형식이거나 복합 형식일 수 있습니다. 들어오는 데이터를 대상에 매핑하는 것은 데이터의 크기나 복잡성에 관계없이 자주 반복되는 시나리오이므로 MVC의 훌륭한 기능입니다. MVC는 바인딩을 추상화하여 이 문제를 해결하므로 개발자는 모든 앱에서 동일한 코드의 약간 다른 버전을 다시 작성하지 않아도 됩니다. 형식 변환기 코드에 사용자 고유의 텍스트를 작성하는 것은 지루하고 오류가 발생하기 쉽습니다.

## <a name="how-model-binding-works"></a>모델 바인딩의 작동 방식

MVC는 HTTP 요청을 받으면 이를 컨트롤러의 특정 작업 메서드에 라우팅합니다. 경로 데이터에 기반하여 실행할 작업 메서드를 결정한 다음, HTTP 요청의 값을 해당 작업 메서드의 매개 변수에 바인드합니다. 예를 들어 다음 URL을 가정해 봅니다.

`http://contoso.com/movies/edit/2`

경로 템플릿은 `{controller=Home}/{action=Index}/{id?}`와 같으므로, `movies/edit/2`는 `Movies` 컨트롤러와 해당 `Edit` 작업 메서드로 라우팅합니다. 또한 `id`라는 선택적 매개 변수를 허용합니다. 작업 메서드의 코드는 다음과 같아야 합니다.

```csharp
public IActionResult Edit(int? id)
   ```

참고: URL 경로에 있는 문자열은 대/소문자를 구분하지 않습니다.

MVC는 이름을 기준으로 요청 데이터를 작업 매개 변수에 바인딩하려고 시도합니다. MVC는 매개 변수 이름과 설정 가능한 공용 속성 이름을 사용하여 각 매개 변수의 값을 찾습니다. 위의 예에서 유일한 작업 매개 변수의 이름은 `id`이며 MVC는 경로 값에서 같은 이름의 값에 바인딩합니다. 경로 값 외에도, MVC는 요청의 여러 부분에서 설정된 순서대로 데이터를 바인딩합니다. 아래는 모델 바인딩이 보이는 순서대로 나열된 데이터 원본 목록입니다.

1. `Form values`: POST 메서드를 사용하여 HTTP 요청에 포함되는 양식 값입니다. (jQuery POST 요청 포함).

2. `Route values`: [라우팅](xref:fundamentals/routing)에서 제공한 경로 값 집합

3. `Query strings`: URI의 쿼리 문자열 부분입니다.

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

참고: 양식 값, 경로 데이터 및 쿼리 문자열은 모두 이름-값 쌍으로 저장됩니다.

모델 바인딩에서는 `id`라는 이름의 키를 요청했고 양식 값에 `id`라는 항목이 없으므로 해당 키를 찾는 경로 값으로 이동했습니다. 이 예제에서는 일치 항목입니다. 바인딩이 발생하고 값이 정수 2로 변환됩니다. 편집(문자열 ID)을 사용하는 동일한 요청이 문자열 "2"로 변환됩니다.

지금까지 예에서는 단순 형식을 사용합니다. MVC에서 단순 형식은 모든 .NET 기본 형식 또는 문자열 형식 변환기를 사용한 형식입니다. 작업 메서드의 매개 변수가 `Movie` 형식과 같은 클래스이고 속성으로 단순 및 복합 형식을 모두 포함하는 경우, MVC의 모델 바인딩에서 이를 원활하게 처리합니다. 리플렉션 및 재귀를 사용하여 복합 형식의 속성을 검색하고 일치하는 항목을 찾습니다. 모델 바인딩은 값을 속성에 바인딩하기 위해 *parameter_name.property_name* 패턴을 찾습니다. 이 양식의 일치하는 값을 찾지 못하면 속성 이름만 사용하여 바인딩을 시도합니다. `Collection` 형식과 같은 형식에 대해, 모델 바인딩에서는 *parameter_name[index]* 또는 *[index]* 에 대해 일치 항목을 찾습니다. 모델 바인딩에서는 `Dictionary` 형식을 유사하게 처리하며 키가 단순 형식인 경우, *parameter_name[key]* 또는 *[key]* 를 요청합니다. 지원되는 키는 동일한 모델 형식에 대해 생성된 필드 이름 HTML 및 태그 도우미와 일치합니다. 이렇게 하면 라운드트립 값이 가능해지므로 생성 또는 편집에서 바인딩된 데이터가 유효성 검사를 통과하지 못했을 때와 같이, 사용자 편의를 위해 양식 필드는 사용자 입력으로 채워져 있습니다.

모델 바인딩을 가능하게 하려면 클래스에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다. 모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화된 다음, 속성을 설정할 수 있습니다.

매개 변수가 바인딩되면 모델 바인딩은 해당 이름의 값을 찾는 것을 중지하고 다음 매개 변수를 바인딩하도록 이동합니다. 그렇지 않은 경우 기본 모델 바인딩 동작은 해당 형식에 따라 매개 변수를 기본값으로 설정합니다.

* `T[]`: `byte[]` 형식의 배열을 제외하고, 바인딩은 `T[]` 형식의 매개 변수를 `Array.Empty<T>()`로 설정합니다. `byte[]` 형식의 배열은 `null`로 설정됩니다.

* 참조 형식: 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 클래스의 인스턴스를 만듭니다. 그러나 모델 바인딩에서는 `string` 매개 변수를 `null`로 설정합니다.

* Nullable 형식: Nullable 형식은 `null`로 설정됩니다. 위의 예제에서 모델 바인딩은 `int?` 형식이므로 `id`를 `null`로 설정합니다.

* 값 형식: Null을 허용하지 않는 값 형식 `T`는 `default(T)`로 설정됩니다. 예를 들어 모델 바인딩은 `int id` 매개 변수를 0으로 설정합니다. 기본값에 의존하지 않고 모델 유효성 검사 또는 nullable 형식을 사용하는 것이 좋습니다.

바인딩에 실패하는 경우 MVC는 오류를 throw하지 않습니다. 사용자 입력을 허용하는 모든 작업에서 `ModelState.IsValid` 속성을 확인해야 합니다.

참고: 컨트롤러의 `ModelState` 속성에 있는 각 항목은 `Errors` 속성이 포함된 `ModelStateEntry`입니다. 이 컬렉션을 직접 쿼리할 필요는 거의 없습니다. 대신 `ModelState.IsValid`를 사용하세요.

또한 모델 바인딩을 수행할 때 MVC가 고려해야 하는 몇 가지 특수 데이터 형식이 있습니다.

* `IFormFile`, `IEnumerable<IFormFile>`: HTTP 요청의 일부인 하나 이상의 업로드된 파일입니다.

* `CancellationToken`: 비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.

이러한 형식은 작업 매개 변수 또는 클래스 형식의 속성에 바인딩할 수 있습니다.

모델 바인딩이 완료되면 [유효성 검사](validation.md)가 발생합니다. 기본 모델 바인딩은 대부분의 개발 시나리오에서 잘 작동합니다. 또한 확장 가능하므로 고유한 요구 사항이 있는 경우 기본 제공 동작을 사용자 지정할 수 있습니다.

## <a name="customize-model-binding-behavior-with-attributes"></a>특성으로 모델 바인딩 동작 사용자 지정

MVC에는 기본 모델 바인딩 동작을 다른 원본으로 전달하는 데 사용할 수 있는 몇 가지 특성이 포함되어 있습니다. 예를 들어 속성에 바인딩이 필요한지, `[BindRequired]` 또는 `[BindNever]` 특성을 사용하여 아예 바인딩이 발생하지 않아야 하는지 여부를 지정할 수 있습니다. 또는 기본 데이터 원본을 재정의하고 모델 바인더의 데이터 원본을 지정할 수 있습니다. 다음은 모델 바인딩 특성 목록입니다.

* `[BindRequired]`: 이 특성은 바인딩이 발생할 수 없는 경우 모델 상태 오류를 추가합니다.

* `[BindNever]`: 모델 바인더에 이 매개 변수에 바인딩하지 않도록 지시합니다.

* `[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: 적용하려는 정확한 바인딩 소스를 지정할 때 사용합니다.

* `[FromServices]`: 이 특성은 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용하여 서비스에서 매개 변수를 바인딩합니다.

* `[FromBody]`: 구성된 포맷터를 사용하여 요청 본문에서 데이터를 바인딩합니다. 포맷터는 요청의 콘텐츠 형식에 따라 선택됩니다.

* `[ModelBinder]`: 기본 모델 바인더, 바인딩 소스 및 이름을 재정의하는 데 사용됩니다.

특성은 모델 바인딩의 기본 동작을 재정의해야 할 때 매우 유용한 도구입니다.

## <a name="customize-model-binding-and-validation-globally"></a>모델 바인딩 사용자 지정 및 전역으로 유효성 검사

모델 바인딩 및 시스템 동작의 유효성 검사는 다음을 설명하는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다.

* 모델이 바인딩되는 방법
* 형식 및 해당 속성에서 유효성 검사가 발생하는 방법

[MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders)에 세부 정보 공급자를 추가하여 시스템의 동작 측면을 전역적으로 구성할 수 있습니다. MVC에는 특정 형식에 대한 모델 바인딩 또는 유효성 검사 비활성화와 같은 동작 구성을 허용하는 몇 가지 기본 제공 정보 공급자가 있습니다.

특정 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider)를 추가합니다. 예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

특정 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에 [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider)를 추가합니다. 예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a>요청 본문에서 형식이 지정된 데이터 바인딩

요청 데이터는 JSON, XML 및 기타 여러 가지 형식으로 제공될 수 있습니다. [FromBody] 특성을 사용하여 매개 변수를 요청 본문의 데이터에 바인딩하려는 것을 표시하는 경우, MVC는 구성된 포맷터 집합을 사용하여 해당 콘텐츠 형식을 기반으로 요청 데이터를 처리합니다. 기본적으로 MVC에는 JSON 데이터를 처리하기 위한 `JsonInputFormatter` 클래스가 포함되어 있지만 XML 및 기타 사용자 지정 형식을 처리하기 위한 포맷터를 더 추가할 수 있습니다.

> [!NOTE]
> `[FromBody]`로 데코레이팅된 작업당 최대 하나의 매개 변수가 있을 수 있습니다. ASP.NET Core MVC 런타임은 요청 스트림을 읽는 책임을 포맷터에 위임합니다. 매개 변수에 대해 요청 스트림을 읽은 후에는, 일반적으로 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 요청 스트림을 다시 읽을 수 없습니다.

> [!NOTE]
> `JsonInputFormatter`은 기본 포맷터이며 [Json.NET](https://www.newtonsoft.com/json)을 기반으로 합니다.

ASP.NET Core는 다르게 지정되는 특성이 적용되지 않는 한, [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) 헤더와 매개 변수의 형식을 기반으로 입력 포맷터를 선택합니다. XML 또는 다른 형식을 사용하려면 *Startup.cs* 파일에서 구성해야 하지만 NuGet을 사용하여 먼저 `Microsoft.AspNetCore.Mvc.Formatters.Xml`에 대한 참조를 얻어야 할 수도 있습니다. 시작 코드는 다음과 비슷합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

*Startup.cs* 파일의 코드에는 ASP.NET Core 앱용 서비스를 빌드하는 데 사용할 수 있는 `services` 인수가 있는 `ConfigureServices` 메서드가 포함되어 있습니다. 이 샘플에서는 MVC가 이 앱에 대해 제공할 서비스로 XML 포맷터를 추가하고 있습니다. `AddMvc` 메서드에 전달된 `options` 인수를 통해 앱 시작 시 MVC의 필터, 포맷터 및 기타 시스템 옵션을 추가하고 관리할 수 있습니다. 그런 다음, 컨트롤러 클래스나 작업 메서드에 `Consumes` 특성을 적용하여 원하는 형식으로 작업합니다.

### <a name="custom-model-binding"></a>사용자 지정 모델 바인딩

사용자 고유의 사용자 지정 모델 바인더를 작성하여 모델 바인딩을 확장할 수 있습니다. [사용자 모델 바인딩](../advanced/custom-model-binding.md)에 대해 자세히 알아보세요.
