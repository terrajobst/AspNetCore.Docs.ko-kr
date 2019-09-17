---
title: 웹 API 분석기 사용
author: pranavkm
description: ASP.NET Core MVC 웹 API 분석기 패키지에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1568eb0304a58758caa5f82249dc42872f5c36b9
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384864"
---
# <a name="use-web-api-analyzers"></a>웹 API 분석기 사용

ASP.NET Core 2.2 이상에서는 웹 API 프로젝트와 함께 사용하기 위한 MVC 분석기 패키지를 제공합니다. 분석기는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러와 함께 작동하지만 [웹 API 규칙](xref:web-api/advanced/conventions)으로 빌드됩니다.

분석기 패키지는 다음을 수행하는 컨트롤러 작업을 모두 알려줍니다.

* 선언되지 않은 상태 코드를 반환
* 선언되지 않은 성공 결과를 반환
* 반환되지 않은 상태 코드를 문서화
* 명시적 모델 유효성 검사를 포함

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>분석기 패키지 참조

ASP.NET Core 3.0 이상에서 분석기는 .NET Core SDK에 포함되어 있습니다. 프로젝트에서 분석기를 사용하도록 설정하려면 프로젝트 파일에 `IncludeOpenAPIAnalyzers` 속성을 포함합니다.

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>패키지 설치

다음 방법 중 하나를 사용하여 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 패키지를 설치합니다.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**패키지 관리자 콘솔** 창에서:
  * **보기** > **다른 창** > **패키지 관리자 콘솔**로 이동합니다.
  * *ApiConventions.csproj* 파일이 위치한 디렉터리로 이동합니다.
  * 다음 명령을 실행합니다.

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **Solution Pad** > **패키지 추가**에서 *패키지* 폴더를 마우스 오른쪽 단추로 클릭합니다.
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

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>웹 API 규칙용 분석기

OpenAPI 문서에는 작업이 반환할 수 있는 상태 코드 및 응답 형식이 포함되어 있습니다. ASP.NET Core MVC에서는 <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 및 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 와 같은 특성을 사용하여 작업을 문서화합니다. <xref:tutorials/web-api-help-pages-using-swagger>는 웹 API 문서화에 대해 자세히 설명합니다.

패키지의 분석기 중 하나는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러를 검사하고 응답을 완전히 문서화하지 않은 작업을 식별합니다. 다음 예제를 참조하세요.

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

앞의 작업은 HTTP 200 성공 반환 유형을 문서화하지만 HTTP 404 실패 상태 코드는 문서화하지 않습니다. 분석기는 HTTP 404 상태 코드에 대한 누락된 설명서를 경고로 보고합니다. 문제를 해결하기 위한 옵션이 제공됩니다.

![경고를 보고하는 분석기](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>추가 자료

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
