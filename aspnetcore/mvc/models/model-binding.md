---
title: ASP.NET Core의 모델 바인딩
author: tdykstra
description: ASP.NET Core에서 모델 바인딩의 작동 방법 및 해당 동작을 사용자 지정하는 방법을 알아봅니다.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 10a9f8327bf16d11ec1e04ac3888d701f1ab1778
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724535"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core의 모델 바인딩

이 문서는 모델 바인딩이 무엇인지, 작동 방법 및 해당 동작을 사용자 지정하는 방법을 설명합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="what-is-model-binding"></a>모델 바인딩이란

컨트롤러 및 Razor 페이지는 HTTP 요청에서 제공되는 데이터를 사용하여 작동합니다. 예를 들어 경로 데이터는 레코드 키를 제공할 수 있으며, 게시된 양식 필드는 모델의 속성에 대한 값을 제공할 수 있습니다. 이러한 각 값을 검색하고 문자열에서 .NET 형식으로 변환하도록 코드를 작성하는 것은 번거롭고 오류가 발생하기 쉽습니다. 모델 바인딩은 이 프로세스를 자동화합니다. 모델 바인딩 시스템:

* 경로 데이터, 양식 필드 및 쿼리 문자열과 같은 다양한 원본의 데이터를 검색합니다.
* 메서드 매개 변수 및 공용 속성에서 컨트롤러 및 Razor 페이지에 데이터를 제공합니다.
* 문자열 데이터를 .NET 형식으로 변환합니다.
* 복합 형식의 속성을 업데이트합니다.

## <a name="example"></a>예

다음 작업 메서드를 사용합니다.

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

앱은 이 URL로 요청을 수신합니다.

```
http://contoso.com/api/pets/2?DogsOnly=true
```

모델 바인딩은 라우팅 시스템이 작업 메서드를 선택한 후 다음 단계를 통해 진행됩니다.

* `GetByID`의 첫 번째 매개 변수, `id`라는 정수를 찾습니다.
* HTTP 요청에서 사용 가능한 원본을 찾고 경로 데이터에서 `id` = "2"를 찾습니다.
* 문자열 "2"를 정수 2로 변환합니다.
* `GetByID`의 다음 매개 변수, `dogsOnly`라는 부울을 찾습니다.
* 원본을 찾고 쿼리 문자열에서 "DogsOnly=true"를 찾습니다. 이름 일치는 대/소문자를 구분하지 않습니다.
* 문자열 "true"를 부울 `true`로 변환합니다.

그런 다음, 프레임워크는 `id` 매개 변수에 대해 2를 `dogsOnly` 매개 변수에 대해 `true`를 전달하는 `GetById` 메서드를 호출합니다.

앞의 예제에서 모델 바인딩 대상은 단순 형식인 메서드 매개 변수입니다. 대상은 복합 형식의 속성일 수도 있습니다. 각 속성이 성공적으로 바인딩된 후 [모델 유효성 검사](xref:mvc/models/validation)가 해당 속성에 대해 발생합니다. 모델에 바인딩되는 데이터의 레코드 및 모든 바인딩 또는 유효성 검사 오류는 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 또는 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)에 저장됩니다. 이 프로세스가 성공되었는지 확인하기 위해 앱은 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 플래그를 확인합니다.

## <a name="targets"></a>대상

모델 바인딩은 다음 종류의 대상에 대한 값을 찾으려고 합니다.

* 요청이 라우팅되는 컨트롤러 작업 메서드의 매개 변수
* 요청이 라우팅되는 Razor Pages 처리기 메서드의 매개 변수 
* 특성으로 지정되는 경우 컨트롤러 또는 `PageModel` 클래스의 공용 속성

### <a name="bindproperty-attribute"></a>[BindProperty] 특성

모델 바인딩이 해당 속성을 대상으로 하도록 컨트롤러의 공용 속성 또는 `PageModel` 클래스에 적용할 수 있습니다.

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a>[BindProperties] 특성

ASP.NET Core 2.1 이상에서 사용할 수 있습니다.  모델 바인딩이 클래스의 모든 공용 속성을 대상으로 하도록 컨트롤러 또는 `PageModel` 클래스에 적용할 수 있습니다.

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>HTTP GET 요청에 대한 모델 바인딩

기본적으로 속성은 HTTP GET 요청에 대해 바인딩되지 않습니다. 일반적으로 GET 요청에 대해 필요한 것은 레코드 ID 매개 변수입니다. 레코드 ID는 데이터베이스에 있는 항목을 찾는 데 사용됩니다. 따라서 모델의 인스턴스를 포함하는 속성을 바인딩할 필요가 없습니다. 속성을 GET 요청의 데이터에 바인딩하려는 시나리오에서 `SupportsGet` 속성을 `true`로 설정합니다.

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>원본

기본적으로 모델 바인딩은 HTTP 요청의 다음 원본에서 키-값 쌍의 양식으로 데이터를 가져옵니다.

1. 양식 필드 
1. 요청 본문([[ApiController] 특성이 있는 컨트롤러](xref:web-api/index#binding-source-parameter-inference)의 경우)
1. 경로 데이터
1. 쿼리 문자열 매개 변수
1. 업로드된 파일 

각 대상 매개 변수 또는 속성의 경우 원본은 이 목록에 표시된 순서대로 검사됩니다. 몇 가지 예외도 있습니다.

* 경로 데이터 및 쿼리 문자열 값은 단순 형식에 대해서만 사용됩니다.
* 업로드된 파일은 `IFormFile` 또는 `IEnumerable<IFormFile>`을 구현하는 대상 유형에만 바인딩됩니다.

기본 동작이 올바른 결과를 제공하지 않는 경우 다음 특성 중 하나를 사용하여 지정된 대상을 사용하도록 원본을 지정할 수 있습니다. 

* [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - 쿼리 문자열에서 값을 가져옵니다. 
* [[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - 경로 데이터에서 값을 가져옵니다.
* [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - 게시된 양식 필드에서 값을 가져옵니다.
* [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 요청 본문에서 값을 가져옵니다.
* [[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP 헤더에서 값을 가져옵니다.

이러한 특성:

* 다음 예제와 같이 모델 속성(모델 클래스가 아닌)에 개별적으로 추가됩니다.

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* 필요에 따라 생성자에서 모델 이름 값을 허용합니다. 이 옵션은 속성 이름이 요청의 값과 일치하지 않는 경우에 제공됩니다. 예를 들어 요청의 값은 다음 예제와 같이 해당 이름에 하이픈이 있는 헤더일 수 있습니다.

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] 특성

요청 본문 데이터는 요청의 콘텐츠 형식과 관련된 입력 포맷터를 사용하여 구문 분석됩니다. 입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.

작업 메서드당 둘 이상의 매개 변수에 `[FromBody]`를 적용하지 마십시오. ASP.NET Core 런타임은 요청 스트림을 읽는 책임을 입력 포맷터에 위임합니다. 요청 스트림을 읽으면 더 이상 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 다시 읽을 수 없습니다.

### <a name="additional-sources"></a>추가 원본

원본 데이터는 *값 공급 기업*에 의해 모델 바인딩 시스템에 제공됩니다. 다른 원본에서 모델 바인딩에 대한 데이터를 가져오는 사용자 지정 값 공급 기업을 작성 및 등록할 수 있습니다. 예를 들어 쿠키 또는 세션 상태의 데이터를 원할 수 있습니다. 새 원본에서 데이터를 가져오려면 다음을 수행합니다.

* `IValueProvider`를 구현하는 클래스를 만듭니다.
* `IValueProviderFactory`를 구현하는 클래스를 만듭니다.
* `Startup.ConfigureServices`에서 팩터리 클래스를 등록합니다.

샘플 앱은 쿠키에서 값을 가져오는 [값 공급 기업](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) 및 [팩터리](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) 예제를 포함합니다. 다음은 `Startup.ConfigureServices`의 등록 코드입니다.

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

표시된 코드는 사용자 지정 값 공급 기업을 모든 기본 제공 값 공급 기업 다음에 배치합니다.  목록에서 첫 번째로 지정하려면 `Add` 대신에 `Insert(0, new CookieValueProviderFactory())`를 호출합니다.

## <a name="no-source-for-a-model-property"></a>모델 속성에 대한 원본 없음

기본적으로 모델 속성에 대한 값이 없으면 모델 상태 오류가 생성되지 않습니다. 속성은 Null 또는 기본값으로 설정됩니다.

* Nullable 단순 형식은 `null`로 설정됩니다.
* Null을 허용하지 않는 값 형식은 `default(T)`로 설정됩니다. 예를 들어 매개 변수 `int id`는 0으로 설정됩니다.
* 복합 형식의 경우 모델 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 인스턴스를 만듭니다.
* 배열은 `byte[]` 배열이 `null`로 설정되는 점을 제외하고 `Array.Empty<T>()`로 설정됩니다.

모델 속성에 대한 양식 필드에 아무것도 없을 때 모델 상태가 무효화되어야 하는 경우 [[BindRequired] 특성](#bindrequired-attribute)을 사용합니다.

이 `[BindRequired]` 동작은 요청 본문의 JSON 또는 XML 데이터가 아니라 게시된 양식 데이터의 모델 바인딩에 적용됩니다. 요청 본문 데이터는 [입력 포맷터](#input-formatters)에서 처리됩니다.

## <a name="type-conversion-errors"></a>형식 변환 오류

원본이 있지만 대상 형식으로 변환될 수 없는 경우 모델 상태는 잘못된 것으로 플래그가 지정됩니다. 이전 섹션에서 설명한 것처럼 대상 매개 변수 또는 속성은 Null 또는 기본값으로 설정됩니다.

`[ApiController]` 특성이 있는 API 컨트롤러에서 잘못된 모델 상태로 자동 HTTP 400 응답이 발생합니다.

Razor 페이지에서 페이지를 오류 메시지와 함께 다시 표시합니다.

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

클라이언트 쪽 유효성 검사는 Razor Pages 양식으로 제출될 수 있는 대부분의 잘못된 데이터를 catch합니다. 이 유효성 검사는 위의 강조 표시된 코드를 트리거하기 어렵게 만듭니다. 샘플 앱은 **Hire Date** 필드에 잘못된 데이터를 배치하고 양식을 제출하는 **잘못된 데이터로 제출** 단추를 포함합니다. 이 단추는 데이터 변환 오류가 발생하는 경우 페이지를 다시 표시하기 위해 코드가 작동하는 방식을 보여 줍니다.

위의 코드로 페이지가 다시 표시될 때 잘못된 입력은 양식 필드에 표시되지 않습니다. 이는 모델 속성이 Null 또는 기본값으로 설정되었기 때문입니다. 잘못된 입력은 오류 메시지에 표시됩니다. 그러나 양식 필드에 잘못된 데이터를 다시 표시하려는 경우 모델 속성을 문자열로 만들고 데이터 변환을 수동으로 수행하는 것이 좋습니다.

형식 변환 오류를 모델 상태 오류로 만들려 하지 않는 경우 동일한 전략을 권장합니다. 이 경우 모델 속성을 문자열로 만듭니다.

## <a name="simple-types"></a>단순 형식

모델 바인더에서 원본 문자열을 변환할 수 있는 단순 형식은 다음을 포함합니다.

* [Boolean](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [Version](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>복합 형식

복합 형식에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다. 모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화됩니다. 

복합 형식의 각 속성의 경우 모델 바인딩은 이름 패턴*prefix.property_name*에 대한 원본을 찾습니다. 아무것도 없는 경우 접두사 없이 *property_name*만을 찾습니다.

매개 변수에 대한 바인딩의 경우 접두사는 매개 변수 이름입니다. `PageModel` 공용 속성에 대한 바인딩의 경우 접두사는 공용 속성 이름입니다. 일부 특성에는 매개 변수의 기본 사용 또는 속성 이름을 재정의할 수 있도록 하는 `Prefix` 속성이 있습니다.

예를 들어 복합 형식이 다음 `Instructor` 클래스라고 가정합니다.

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>접두사 = 매개 변수 이름

바인딩될 모델이 `instructorToUpdate`라는 매개 변수인 경우:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

모델 바인딩은 키 `instructorToUpdate.ID`에 대한 원본을 찾아 시작합니다. 없는 경우 접두사 없이 `ID`를 찾습니다.

### <a name="prefix--property-name"></a>접두사 = 속성 이름

바인딩될 모델이 컨트롤러 또는 `PageModel` 클래스의 `Instructor`라는 속성인 경우:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다. 없는 경우 접두사 없이 `ID`를 찾습니다.

### <a name="custom-prefix"></a>사용자 지정 접두사

바인딩될 모델이 `instructorToUpdate`라는 매개 변수이고 `Bind` 특성이 접두사로 `Instructor`를 지정하는 경우:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다. 없는 경우 접두사 없이 `ID`를 찾습니다.

### <a name="attributes-for-complex-type-targets"></a>복합 형식 대상에 대한 특성

여러 기본 제공 특성은 복합 형식의 모델 바인딩을 제어할 수 있습니다.

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> 이러한 특성은 게시된 양식 데이터가 값의 원본일 때 모델 바인딩에 영향을 줍니다. 게시된 JSON 및 XML 요청 본문을 처리하는 입력 포맷터에는 영향을 주지 않습니다. 입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.
>
> [모델 유효성 검사](xref:mvc/models/validation#required-attribute)에서 `[Required]` 특성의 설명을 참조하세요.

### <a name="bindrequired-attribute"></a>[BindRequired] 특성

메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다. 모델의 속성에 대한 바인딩이 발생할 수 없는 경우 모델 바인딩이 모델 상태 오류를 추가하도록 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever] 특성

메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다. 모델 바인딩이 모델의 속성을 설정하는 것을 방지합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[Bind] 특성

클래스 또는 메서드 매개 변수에 적용될 수 있습니다. 모델 바인딩에 포함되어야 하는 모델의 속성을 지정합니다.

다음 예제에서 모든 처리기 또는 작업 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

다음 예제에서 `OnPost` 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]` 특성은 *만들기* 시나리오에서 초과 게시를 방지하는 데 사용될 수 있습니다. 제외된 속성은 변경되지 않은 채로 남겨지는 대신 Null 또는 기본값으로 설정되기 때문에 편집 시나리오에서 잘 작동하지 않습니다. 초과 게시에 대한 방어의 경우 뷰 모델이 `[Bind]` 특성보다 권장됩니다. 자세한 내용은 [초과 게시에 대한 보안 정보](xref:data/ef-mvc/crud#security-note-about-overposting)를 참조하세요.

## <a name="collections"></a>컬렉션

단순 형식의 컬렉션인 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다. 일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다. 예:

* 바인딩되는 매개 변수가 `selectedCourses`라는 배열이라고 가정합니다.

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* 양식 또는 쿼리 문자열 데이터는 다음 형식 중 하나일 수 있습니다.
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* 다음 형식은 양식 데이터에서만 지원됩니다.

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* 앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 배열을 `selectedCourses` 매개 변수에 전달합니다.

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  아래 첨자 숫자(... [0] ... [1] ...)를 사용하는 데이터 형식은 0부터 시작하여 순차적으로 번호가 매겨지는지 확인해야 합니다. 아래 첨자 번호에 간격이 있는 경우 간격 뒤에 있는 모든 항목은 무시됩니다. 예를 들어 아래 첨자가 0과 1 대신 0과 2인 경우 두 번째 항목은 무시됩니다.

## <a name="dictionaries"></a>사전

`Dictionary` 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다. 일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다. 예:

* 대상 매개 변수가 `selectedCourses`라는 `Dictionary<int, string>`라고 가정합니다.

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* 게시된 양식 또는 쿼리 문자열 데이터는 다음 예제 중 하나와 같을 수 있습니다.

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* 앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 사전을 `selectedCourses` 매개 변수에 전달합니다.

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

## <a name="special-data-types"></a>특수 데이터 형식

모델 바인딩이 처리할 수 있는 일부 특수 데이터 형식이 있습니다.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile 및 IFormFileCollection

HTTP 요청에 포함되는 업로드된 파일입니다.  또한 여러 파일에 대해 `IEnumerable<IFormFile>`이 지원됩니다.

### <a name="cancellationtoken"></a>CancellationToken

비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.

### <a name="formcollection"></a>FormCollection

게시된 양식 데이터에서 모든 값을 검색하는 데 사용됩니다.

## <a name="input-formatters"></a>입력 포맷터

요청 본문의 데이터는 JSON, XML 또는 일부 다른 형식일 수 있습니다. 이 데이터를 구문 분석하기 위해 모델 바인딩은 특정 콘텐츠 유형을 처리하도록 구성된 *입력 포맷터*를 사용합니다. 기본적으로 ASP.NET Core는 JSON 데이터를 처리하기 위한 JSON 기반 입력 포맷터를 포함합니다. 다른 콘텐츠 형식에 대해 다른 포맷터를 추가할 수 있습니다.

ASP.NET Core는 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 특성을 기반으로 입력 포맷터를 선택합니다. 특성이 없는 경우 [Content-Type 헤더](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)를 사용합니다.

기본 제공 XML 입력 포맷터를 사용하려면 다음을 수행합니다.

* `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지를 설치합니다.

* `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 또는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출합니다.

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* 요청 본문에서 XML을 필요로 하는 컨트롤러 클래스 또는 작업 메서드에 `Consumes` 특성을 적용합니다.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  자세한 내용은 [XML Serialization 소개](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization)를 참조하세요.

## <a name="exclude-specified-types-from-model-binding"></a>모델 바인딩에서 지정된 형식 제외

모델 바인딩 및 시스템 동작의 유효성 검사는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다. [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)에 세부 정보 공급 기업을 추가하여 `ModelMetadata`를 사용자 지정할 수 있습니다. 기본 제공 세부 정보 공급 기업을 지정된 형식에 대한 모델 바인딩 또는 유효성 검사를 비활성화하기 위해 사용할 수 있습니다.

지정된 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>를 추가합니다. 예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

지정된 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>를 추가합니다. 예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>사용자 지정 모델 바인더

사용자 지정 모델 바인더를 작성하고 지정된 대상에 대해 선택하도록 `[ModelBinder]` 특성을 사용하여 모델 바인딩을 확장할 수 있습니다. [사용자 모델 바인딩](xref:mvc/advanced/custom-model-binding)에 대해 자세히 알아보세요.

## <a name="manual-model-binding"></a>수동 모델 바인딩

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 메서드를 사용하여 모델 바인딩을 수동으로 호출할 수 있습니다. 메서드는 `ControllerBase` 및 `PageModel` 클래스에서 정의됩니다. 메서드 오버로드를 통해 사용할 접두사 및 값 공급 기업을 지정할 수 있습니다. 모델 바인딩이 실패하는 경우 메서드는 `false`를 반환합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices] 특성

이 특성의 이름은 데이터 원본을 지정하는 모델 바인딩 특성의 패턴을 따릅니다. 그러나 값 공급 기업의 바인딩 데이터에 대한 것은 아닙니다. [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 형식의 인스턴스를 가져옵니다. 특정 메서드가 호출되는 경우에만 서비스가 필요할 때 생성자 주입에 대안을 제공하는 것이 목적입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
