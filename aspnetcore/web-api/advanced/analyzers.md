---
title: 웹 API 분석기 사용
author: pranavkm
description: Microsoft.AspNetCore.Mvc.Api.Analyzers의 웹 API 분석기에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 2aaef738ab2a64f85cb85708f63d2375c04cacb5
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538561"
---
# <a name="use-web-api-analyzers"></a>웹 API 분석기 사용

ASP.NET Core 2.2 이상에는 웹 API용 분석기가 포함된 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 패키지가 포함되어 있습니다. 분석기는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러와 함께 작동하지만 [API 규칙](xref:web-api/advanced/conventions)으로 빌드됩니다.

## <a name="package-installation"></a>패키지 설치

`Microsoft.AspNetCore.Mvc.Api.Analyzers`는 다음 방법 중 하나를 사용하여 추가할 수 있습니다.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **패키지 관리자 콘솔** 창에서:
  * **보기** > **다른 창** > **패키지 관리자 콘솔**로 이동합니다.
  * *ApiConventions.csproj* 파일이 위치한 디렉터리로 이동합니다.
  * 다음 명령을 실행합니다.

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* **NuGet 패키지 관리** 대화 상자에서:
  * **솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
  * **패키지 소스**를 “nuget.org”로 설정합니다.
  * 검색 상자에 "Microsoft.AspNetCore.Mvc.Api.Analyzers"를 입력합니다.
  * **찾아보기** 탭에서 "Microsoft.AspNetCore.Mvc.Api.Analyzers" 패키지를 선택하고 **설치**를 클릭합니다.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **Solution Pad** > **패키지 추가...** 에서 *패키지* 폴더를 마우스 오른쪽 단추로 클릭합니다.
* **패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정합니다.
* 검색 상자에 "Microsoft.AspNetCore.Mvc.Api.Analyzers"를 입력합니다.
* 결과 창에서 "Microsoft.AspNetCore.Mvc.Api.Analyzers" 패키지를 선택하고 **패키지 추가**를 클릭합니다.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널**에서 다음 명령을 실행합니다.

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

다음 명령을 실행합니다.

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a>API 규칙용 분석기

OpenAPI 문서에는 작업이 반환할 수 있는 상태 코드 및 응답 형식이 포함되어 있습니다. ASP.NET Core MVC에서는 <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 및 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 와 같은 특성을 사용하여 작업을 문서화합니다. <xref:tutorials/web-api-help-pages-using-swagger>는 API 문서화에 대해 자세히 설명합니다.

패키지의 분석기 중 하나는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러를 검사하고 응답을 완전히 문서화하지 않은 작업을 식별합니다. 다음 예제를 참조하세요.

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

앞의 작업은 HTTP 200 성공 반환 유형을 문서화하지만 HTTP 404 실패 상태 코드는 문서화하지 않습니다. 분석기는 HTTP 404 상태 코드에 대한 누락된 설명서를 경고로 보고합니다. 문제를 해결하기 위한 옵션이 제공됩니다.

![경고를 보고하는 분석기](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>추가 자료

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
