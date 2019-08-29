---
title: ASP.NET Core Web API에서 포맷터 사용자 지정
author: rick-anderson
description: ASP.NET Core의 웹 API에서 사용자 지정 포맷터를 만들고 사용하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 6fb7e192bf3e943eb9018b08fb87a833d3643208
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975678"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API에서 포맷터 사용자 지정

작성자: [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC는 JSON 또는 XML을 사용하여 웹 API에서 데이터 교환에 대한 기본 제공 지원을 제공합니다. 이 문서에서는 사용자 지정 포맷터를 만들어 추가 형식에 대한 지원을 추가하는 방법을 보여줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>사용자 지정 포맷터를 사용하는 경우

[콘텐츠 협상](xref:web-api/advanced/formatting#content-negotiation) 프로세스에서 기본 제공 포맷터에 의해 지원되지 않는 콘텐츠 형식을 지원하려는 경우(JSON 및 XML) 사용자 지정 포맷터를 사용합니다.

예를 들어, 웹 API의 클라이언트 중 일부가 [Protobuf](https://github.com/google/protobuf) 형식을 처리할 수 있는 경우 더 효율적이기 때문에 해당 클라이언트에서 Protobuf를 사용합니다. 또는 웹 API에서 연락 데이터를 교환하는 데 자주 사용되는 [vCard](https://wikipedia.org/wiki/VCard) 형식으로 연락처 이름 및 주소를 보낼 수 있습니다. 이 문서에서 제공하는 샘플 앱은 간단한 vCard 포맷터를 구현합니다.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>사용자 지정 포맷터를 사용하는 방법 개요

사용자 지정 포맷터를 만들고 사용하는 단계는 다음과 같습니다.

* 클라이언트에 보낼 데이터를 직렬화하려는 경우 출력 포맷터 클래스를 만듭니다.
* 클라이언트에서 받을 데이터를 deserialize하려는 경우 입력 포맷터 클래스를 만듭니다.
* 포맷터의 인스턴스를 [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)의 `InputFormatters` 및 `OutputFormatters` 컬렉션에 추가합니다.

다음 섹션에서는 이러한 각 단계에 대한 지침 및 코드 예제를 제공합니다.

## <a name="how-to-create-a-custom-formatter-class"></a>사용자 지정 포맷터 클래스를 만드는 방법

포맷터를 만들려면:

* 적절한 기본 클래스에서 클래스를 파생시킵니다.
* 생성자에서 유효한 미디어 형식 및 인코딩을 지정합니다.
* `CanReadType`/`CanWriteType` 메서드 재정의
* `ReadRequestBodyAsync`/`WriteResponseBodyAsync` 메서드 재정의
  
### <a name="derive-from-the-appropriate-base-class"></a>적절한 기본 클래스에서 파생

텍스트 미디어 형식(예: vCard)의 경우 [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) 또는 [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 기본 클래스에서 파생시킵니다.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.

이진 형식의 경우 [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) 또는 [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 기본 클래스에서 파생시킵니다.

### <a name="specify-valid-media-types-and-encodings"></a>유효한 미디어 형식 및 인코딩 지정

생성자에서 `SupportedMediaTypes` 및 `SupportedEncodings` 컬렉션에 추가하여 유효한 미디어 형식 및 인코딩을 지정합니다.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.

> [!NOTE]
> 포맷터 클래스에서 생성자 종속성 주입을 수행할 수 없습니다. 예를 들어 생성자에 로거 매개 변수를 추가하여 로거를 가져올 수 없습니다. 서비스에 액세스하려면 메서드에 전달된 컨텍스트 개체를 사용해야 합니다. [아래](#read-write) 코드 예제에서는 수행 방법을 보여줍니다.

### <a name="override-canreadtypecanwritetype"></a>Override CanReadType/CanWriteType

`CanReadType` 또는 `CanWriteType` 메서드를 재정의하여 deserialize하거나 직렬화할 수 있는 형식을 지정합니다. 예를 들어 `Contact` 형식에서 vCard 텍스트를 만들거나 그 반대로 수행할 수만 있습니다.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.

#### <a name="the-canwriteresult-method"></a>CanWriteResult 메서드

일부 시나리오에서는 `CanWriteType` 대신 `CanWriteResult`를 재정의해야 합니다. 다음 조건이 true인 경우 `CanWriteResult`를 사용합니다.

* 작업 메서드에서는 모델 클래스를 반환합니다.
* 런타임 시 반환될 수 있는 파생 클래스가 있습니다.
* 런타임 시 작업에서 반환하는 파생 클래스를 알아야 합니다.

예를 들어 작업 메서드 서명이 `Person` 형식을 반환한다고 가정하지만 `Person`에서 파생된 `Student` 또는 `Instructor` 형식을 반환할 수 있습니다. 포맷터에서 `Student` 개체만을 처리하려는 경우 `CanWriteResult` 메서드에 제공된 컨텍스트 개체에서 [개체](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) 형식을 확인합니다. 작업 메서드가 `IActionResult`를 반환할 때 반드시 `CanWriteResult`를 사용해야 하는 것은 아닙니다. 이 경우에 `CanWriteType` 메서드는 런타임 형식을 수신합니다.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>ReadRequestBodyAsync/WriteResponseBodyAsync 재정의

`ReadRequestBodyAsync` 또는 `WriteResponseBodyAsync`에서 deserialize하거나 직렬화하는 작업의 실제 작업을 수행합니다. 다음 예제에서 강조 표시된 줄은 종속성 주입 컨테이너에서 서비스를 가져오는 방법을 보여줍니다(생성자 매개 변수에서 가져올 수 없음).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>사용자 지정 포맷터를 사용하도록 MVC를 구성하는 방법

사용자 지정 포맷터를 사용하려면 `InputFormatters` 또는 `OutputFormatters` 컬렉션에 포맷터 클래스의 인스턴스를 추가합니다.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

포맷터는 삽입한 순서 대로 평가됩니다. 첫 번째 포맷터가 우선됩니다.

## <a name="next-steps"></a>다음 단계

* [GitHub의 일반 텍스트 포맷터의 샘플 코드.](https://github.com/aspnet/Entropy/tree/master/samples/Mvc.Formatters)
* 간단한 vCard 입력 및 출력 포맷터를 구현하는 [이 문서의 샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)입니다. 앱은 다음 예제와 같이 vCard를 읽고 씁니다.

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

vCard 출력을 확인하려면 애플리케이션을 실행하고 `http://localhost:63313/api/contacts/`(Visual Studio에서 실행할 때) 또는 `http://localhost:5000/api/contacts/`(명령줄에서 실행할 때)에 수락 헤더 "텍스트/vcard"를 포함하는 Get 요청을 보냅니다.

연락처의 메모리 내 컬렉션에 vCard를 추가하려면 콘텐츠 형식 헤더 "텍스트/vcard" 및 본문의 vCard 텍스트를 포함하는 위의 예제와 같은 형식으로 지정된 동일한 URL에 Post 요청을 보냅니다.
