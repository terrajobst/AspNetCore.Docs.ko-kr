---
title: '자습서: ASP.NET Core를 사용하여, jQuery로 웹 API 호출하기'
author: rick-anderson
description: jQuery를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022553"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>자습서: jQuery로 ASP.NET Core 웹 API 호출하기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 jQuery를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 보여줍니다.

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.2의 경우에는 [jQuery로 웹 API 호출하기](xref:tutorials/first-web-api#call-the-api-with-jquery) 버전 2.2를 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>전제 조건

* [자습서: 웹 API 만들기](xref:tutorials/first-web-api) 완료
* CSS, HTML, JavaScript 및 jQuery에 대한 지식

## <a name="call-the-api-with-jquery"></a>jQuery를 사용하여 API 호출

이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다. jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.

다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.

*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 표시로 콘텐츠를 바꿉니다.

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 코드로 콘텐츠를 바꿉니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.

* *Properties\launchSettings.json*을 엽니다.
* `launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.

여러 가지 방법으로 jQuery를 가져올 수 있습니다. 위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.

이 샘플은 API의 모든 CRUD 메서드를 호출합니다. API 호출에 대한 설명은 다음과 같습니다.

### <a name="get-a-list-of-to-do-items"></a>할 일 항목의 목록 가져오기

jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다. 요청이 성공하면 `success` 콜백 함수가 호출됩니다. 콜백에서 DOM은 할 일 정보로 업데이트됩니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>할 일 항목 추가

[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다. `accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다. [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다. API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>할 일 항목 업데이트

할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다. `url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>할 일 항목 삭제

할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
