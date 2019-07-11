---
title: ASP.NET Core에서 통합 테스트
author: guardrex
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: test/integration-tests
ms.openlocfilehash: a4e22e53b4658a7c6da3c9e15671a355b212f559
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815362"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core에서 통합 테스트

하 여 [Luke Latham](https://github.com/guardrex) 고 [Steve Smith](https://ardalis.com/)

통합 테스트 데이터베이스, 파일 시스템 및 네트워크와 같은 앱의 지원 인프라를 포함 하는 수준에서 앱의 구성 요소가 제대로 작동 하는지 확인 합니다. ASP.NET Core는 테스트 웹 호스트와 메모리 내 테스트 서버를 사용 하 여 단위 테스트 프레임 워크를 사용 하 여 통합 테스트를 지원 합니다.

이 항목에서는 단위 테스트의 기본 지식이 있다고 가정 합니다. 테스트 개념을 잘 알 수 없는 경우 참조를 [Unit Testing.NET Core 및.NET Standard](/dotnet/core/testing/) 항목 및 해당 연결 된 콘텐츠입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 Razor 페이지 앱 및 Razor 페이지의 기본 지식이 있다고 가정 합니다. Razor 페이지를 사용 하 여 알 수 없는 경우 다음 항목을 참조 합니다.

* [Razor 페이지 소개](xref:razor-pages/index)
* [Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 페이지 단위 테스트](xref:test/razor-pages-tests)

> [!NOTE]
> Spa를 테스트 하는 것에 대 한 것이 좋습니다는 도구와 같은 [Selenium](https://www.seleniumhq.org/)는 브라우저를 자동화할 수 있습니다.

## <a name="introduction-to-integration-tests"></a>통합 테스트 소개

보다 광범위 한 수준에서 앱의 구성 요소를 평가 하는 통합 테스트 [단위 테스트](/dotnet/core/testing/)합니다. 단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다. 통합 테스트는 두 개 이상의 앱 구성 요소 수 있는 완벽 하 게 요청을 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상된 결과 생성 하기 위해 함께 작동 하는지 확인 합니다.

이러한 광범위 한 테스트는 앱의 인프라 및 종종 다음 구성 요소를 포함 하는 전체 프레임 워크를 테스트 하는 데 사용 됩니다.

* 데이터베이스
* 파일 시스템
* 네트워크 어플라이언스
* 요청-응답 파이프라인

단위 테스트 라고 위조 사용 하 여 구성 요소 *fakes* 하거나 *모의 개체*, 인프라 구성 요소 대신 합니다.

단위 테스트와 달리 통합 테스트.

* 앱이 프로덕션 환경에서 사용 하는 실제 구성 요소를 사용 합니다.
* 자세한 코드 및 데이터 처리가 필요 합니다.
* 실행 하는 데 오래 걸립니다.

따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트의 사용을 제한 합니다. 단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수, 하는 경우 단위 테스트를 선택 합니다.

> [!TIP]
> 데이터베이스 및 파일 시스템을 사용 하 여 데이터 및 파일 액세스의 모든 가능한 순열에 대 한 통합 테스트를 작성 하지 마십시오. 앱에서 얼마나 많은 배치에 관계 없이 데이터베이스 및 파일 시스템, 읽기, 쓰기, 업데이트 및 삭제 통합 테스트는 일반적으로 적절 하 게 테스트 데이터베이스 수 및 파일 시스템 구성 요소 집합을 집중된 상호 작용 합니다. 이러한 구성 요소 상호 작용 하는 방법 논리의 일상적인 테스트에 대 한 사용 하 여 단위 테스트 합니다. 단위 테스트에서 인프라를 사용 하 여 fakes/mock 결과 테스트 실행이 빨라집니다.

> [!NOTE]
> 테스트 프로젝트는 자주 통합 테스트의 토론에서 호출 합니다 *테스트 대상 시스템*, 또는 줄여서 "SUT"입니다.

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 통합 테스트

ASP.NET Core에서 통합 테스트 하려면 다음 항목이 필요 합니다.

* 테스트 프로젝트를 포함 하 고 테스트를 실행 됩니다. 테스트 프로젝트에 호출 테스트 ASP.NET Core 프로젝트에 대 한 참조를 *테스트 중인 시스템* (SUT). _"SUT" 테스트 앱을 가리키도록이 항목 전체에서 사용 됩니다._
* 테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 요청 및 응답을 SUT 처리 하기 위해 테스트 서버 클라이언트를 사용 합니다.
* 테스트 결과 테스트와 보고서를 실행 하는 test runner는 사용 됩니다.

통합 테스트는 일반적인 포함 하는 이벤트의 순서에 따라 *정렬*하십시오 *Act*, 및 *Assert* 테스트 단계:

1. SUT의 웹 호스트 구성 됩니다.
1. 앱에 대 한 요청을 제출 하는 테스트 서버 클라이언트 생성 됩니다.
1. 합니다 *정렬* 테스트 단계가 실행 됩니다. 테스트 응용 프로그램 요청을 준비합니다.
1. 합니다 *Act* 테스트 단계가 실행 됩니다. 클라이언트 요청을 제출 하 고 응답을 받습니다.
1. 합니다 *Assert* 테스트 단계가 실행 됩니다. *실제* 응답으로 유효성을 검사를 *전달* 또는 *실패* 기반을 *예상* 응답 합니다.
1. 프로세스가 모든 테스트 실행 될 때까지 계속 됩니다.
1. 테스트 결과 보고 됩니다.

일반적으로 테스트에 대 한 앱의 일반적인 웹 호스트를 실행 하는 보다 테스트 웹 호스트를 다르게 구성 됩니다. 예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.

테스트 웹 호스트 및 메모리 내 테스트 서버와 같은 인프라 구성 요소 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), 제공 또는 관리 하는 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지 있습니다. 이 패키지의 사용 하 여 테스트 만들기 및 실행을 간소화합니다.

`Microsoft.AspNetCore.Mvc.Testing` 패키지에는 다음 작업을 처리 합니다.

* 종속성 파일을 복사 ( *\*.deps*)에서 테스트 프로젝트의 SUT *bin* 디렉터리입니다.
* 정적 파일 및 페이지/뷰 테스트를 실행 하는 경우 찾을 수 있도록 SUT의 프로젝트 루트 콘텐츠 루트를 설정 합니다.
* 제공 된 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 사용 하 여 SUT 부트스트래핑 간단 하 게 `TestServer`합니다.

합니다 [단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에는 테스트 프로젝트 및 테스트 러너, 테스트 및 방법에 대 한 권장 사항을 이름 테스트를 실행 하 여 클래스를 테스트 하는 방법에 자세한 지침과 함께 설정 하는 방법에 설명 합니다.

> [!NOTE]
> 앱에 대 한 테스트 프로젝트를 만들 때 다른 프로젝트에 통합 테스트에서 단위 테스트를 구분 합니다. 이 사용이 하면 인프라 테스트 구성 요소 단위 테스트에서 실수로 포함 되지 있는지 확인 합니다. 분리 단위 및 통합 테스트에서는 실행 되는 테스트 집합을 제어 합니다.

차이가 거의 없는 Razor 페이지 앱의 테스트에 대 한 구성 및 MVC 앱입니다. 유일한 차이점은 테스트의 이름을 지정 하는 방법의 경우 Razor 페이지 앱에서 페이지 끝점의 테스트는 일반적으로 명명 된 페이지 모델 클래스 (예를 들어 `IndexPageTests` 인덱스 페이지에 대 한 구성 요소 통합을 테스트). MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스 별로 구성 및 테스트 컨트롤러의 이름을 딴 (예를 들어 `HomeControllerTests` 홈 컨트롤러에 대 한 구성 요소 통합을 테스트).

## <a name="test-app-prerequisites"></a>테스트 앱 필수 구성 요소

테스트 프로젝트는 다음 작업을 수행 해야합니다.

* 다음 패키지를 참조 합니다.
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Web SDK 프로젝트 파일에 지정 (`<Project Sdk="Microsoft.NET.Sdk.Web">`). 웹 SDK를 참조할 때 필요 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.

이러한 필수 구성이 요소에서 볼 수 있습니다 합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)합니다. 검사는 *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일입니다. 샘플 앱에서는 합니다 [xUnit](https://xunit.github.io/) 테스트 프레임 워크와 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리, 샘플 앱 참조 하므로:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 환경

경우는 SUT [환경](xref:fundamentals/environments) 설정 되지 않은 환경에 대 한 기본값으로 개발 합니다.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Basic는 WebApplicationFactory 기본값을 사용 하 여 테스트

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 만드는 데 사용 되는 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 통합 테스트에 대 한 합니다. `TEntryPoint` SUT의 진입점 클래스는 일반적으로 `Startup` 클래스입니다.

테스트 클래스 구현 된 *클래스 fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 나타내고 클래스 테스트가 포함 된 클래스에서 테스트 간에 공유 개체 인스턴스를 제공 합니다.

### <a name="basic-test-of-app-endpoints"></a>앱 끝점의 기본 테스트

클래스를 테스트 `BasicTests`를 사용 하 여는 `WebApplicationFactory` 는 SUT 부트스트랩 제공 하는 [HttpClient](/dotnet/api/system.net.http.httpclient) 테스트 메서드에 `Get_EndpointsReturnSuccessAndCorrectContentType`합니다. 메서드가 성공한 경우 응답 상태 코드를 검사 (범위 200-299 상태 코드) 및 `Content-Type` 헤더는 `text/html; charset=utf-8` 여러 앱 페이지에 대 한 합니다.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 의 인스턴스를 만들고 `HttpClient` 자동 리디렉션을 따릅니다를 쿠키를 처리 합니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

기본적으로 필수적이 지 않은 쿠키 없는 유지를 요청 하는 경우는 [GDPR 승인 정책](xref:security/gdpr) 사용 가능 합니다. 필수적이 지 않은 쿠키 TempData 공급자에 의해 사용 되는 유지 하기 위해 테스트에서 필수로 표시 합니다. 전자 메일 메시지에 쿠키를 필수로 표시 지침을 참조 하세요 [Essential 쿠키](xref:security/gdpr#essential-cookies)합니다.

### <a name="test-a-secure-endpoint"></a>보안 끝점을 테스트 합니다.

다른 테스트는 `BasicTests` 보안 끝점을 인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션하는 클래스를 확인 합니다.

SUT에서 `/SecurePage` 사용 하 여 페이지를 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 적용 하기 위한 규칙을 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 페이지로. 자세한 내용은 [Razor 페이지 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)합니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

에 `Get_SecurePageRequiresAnAuthenticatedUser` 테스트를 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 리디렉션을 허용 하지 않도록 설정 하 여 설정 됩니다 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 에 `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

리디렉션에 따라 클라이언트를 허용 하지 않습니다, 다음 확인을 내릴 수 있습니다.

* SUT에서 반환 된 상태 코드를 확인할 수 있습니다 예상에 대 한 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과 만드는 것이 로그인 페이지로 리디렉션 후 최종 상태 코드가 아닌 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* 합니다 `Location` 응답 헤더에 헤더 값이 시작 하는 것인지 확인 됩니다 `http://localhost/Identity/Account/Login`, 하지 최종 로그인 페이지 응답 위치를 `Location` 헤더 있을 수 없습니다.

에 대 한 자세한 `WebApplicationFactoryClientOptions`를 참조 합니다 [클라이언트 옵션](#client-options) 섹션입니다.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 사용자 지정

웹 호스트 구성에서 상속 하 여 테스트 클래스와 독립적으로 만들 수 있습니다 `WebApplicationFactory` 하나 이상의 사용자 지정 팩터리를 만듭니다.

1. 상속 `WebApplicationFactory` 시키고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)합니다. 합니다 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 사용 하 여 서비스 컬렉션의 구성을 허용 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   데이터베이스에서 시드를 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의해 수행 되는 `InitializeDbForTests` 메서드. 메서드는에 설명 되어는 [통합 테스트 샘플: 테스트 앱 조직을](#test-app-organization) 섹션입니다.

2. 사용자 지정을 사용 하 여 `CustomWebApplicationFactory` 테스트 클래스에 있습니다. 다음 예제에서는에서 팩터리를 `IndexPageTests` 클래스:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   방지 하도록 구성 된 샘플 앱의 클라이언트는 `HttpClient` 에서 다음 리디렉션합니다. 에 설명 된 대로 합니다 [보안 끝점을 테스트](#test-a-secure-endpoint) 섹션에서는 앱의 첫 번째 응답의 결과 확인 하는 테스트를 허용 합니다. 첫 번째 응답은 리디렉션을 사용 하 여 이러한 테스트의 대부분을 `Location` 헤더입니다.

3. 일반적인 테스트를 사용 하는 `HttpClient` 및 요청 및 응답을 처리 하기 위해 도우미 메서드.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT 대 한 모든 POST 요청에는 앱의 자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)입니다. 테스트의 POST 요청을 정렬 하기 위해 앱 테스트 해야 합니다.

1. 페이지에 대 한 요청을 확인 합니다.
1. 위조 방지 쿠키 및 응답에서 요청 유효성 검사 토큰을 구문 분석 합니다.
1. 현재 위치에서 위조 방지 쿠키 및 요청 유효성 검사를 사용 하 여 POST 요청 토큰을 확인 합니다.

합니다 `SendAsync` 도우미 확장 메서드 (*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드 (*Helpers/HtmlHelpers.cs*)에 [샘플앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) 를 사용 합니다 [AngleSharp](https://anglesharp.github.io/) 다음 메서드를 사용 하 여 위조 방지 검사를 처리 하는 파서:

* `GetDocumentAsync` &ndash; 수신 합니다 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 반환 하 고는 `IHtmlDocument`합니다. `GetDocumentAsync` 준비 하는 팩터리를 사용 하는 *가상 응답* 원본을 `HttpResponseMessage`합니다. 자세한 내용은 참조는 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)합니다.
* `SendAsync` 에 대 한 확장 메서드는 `HttpClient` compose는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 호출 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 는 SUT에 요청을 제출 하 합니다. 에 대 한 오버 로드가 `SendAsync` HTML 양식을 수락 (`IHtmlFormElement`) 및 다음:
  * 폼의 단추를 제출 (`IHtmlElement`)
  * 폼 값 컬렉션 (`IEnumerable<KeyValuePair<string, string>>`)
  * 제출 단추 (`IHtmlElement`) 값을 구성 하 고 (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) 타사 구문 분석 하는 데모용으로이 항목에서는 샘플 앱에 사용 되는 라이브러리입니다. AngleSharp는 ASP.NET Core 앱의 통합 테스트에 필요한 또는 지원 되지 않습니다. 다른 파서 사용할 수와 같은 합니다 [Html 민첩성 팩 (HAP)](https://html-agility-pack.net/)합니다. 다른 방법은 위조 방지 시스템의 요청 확인 토큰 및 위조 방지 쿠키를 직접 처리 하는 코드를 작성 하는 것입니다.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder 사용 하 여 클라이언트를 사용자 지정

추가 구성 된 테스트 메서드 내에서 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 만듭니다 `WebApplicationFactory` 와 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 추가로 구성 하 여 사용자 지정 합니다.

합니다 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 의 메서드를 테스트 합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 사용을 보여 줍니다 `WithWebHostBuilder`합니다. 이 테스트는 SUT에서 양식을 제출 하 여을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.

다른 테스트 하기 때문에 `IndexPageTests` 클래스의 모든 데이터베이스에 레코드를 삭제 하 고 전에 실행할 수는 작업을 수행 합니다 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드, 데이터베이스 레코드를 삭제 하려면 SUT에 있는지 확인 하기 위해이 테스트 메서드에 시드는 합니다. 선택 하는 `deleteBtn1` 단추는 `messages` 는 SUT 요청에는 SUT 형태로 시뮬레이션 됩니다:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>클라이언트 옵션

다음 표에서 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 를 만들 때 사용할 수 있는 `HttpClient` 인스턴스.

| 옵션 | 설명 | 기본값 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 가져오거나 여부 `HttpClient` 인스턴스 리디렉션 응답을 자동으로 수행 해야 합니다. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 기본 주소를 가져오거나 설정 합니다. `HttpClient` 인스턴스. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 가져오거나 여부를 `HttpClient` 인스턴스 쿠키를 처리 해야 합니다. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 최대 리디렉션 응답 수를 가져오거나 `HttpClient` 인스턴스 따라야 합니다. | 7 |

만들기는 `WebApplicationFactoryClientOptions` 클래스를 전달 하는 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드 (값이 코드 예제에 표시 되어 기본값):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>모의 서비스를 삽입 합니다.

서비스에 대 한 호출을 사용 하 여 테스트에서 재정의할 수 있습니다 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 호스트 작성기입니다. **모의 서비스를 삽입할는 SUT 있어야를 `Startup` 클래스는 `Startup.ConfigureServices` 메서드.**

샘플 SUT 견적을 반환 하는 범위가 지정 된 서비스를 포함 합니다. 견적은 인덱스 페이지가 요청 될 때 인덱스 페이지의 숨겨진된 필드에 포함 됩니다.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

서비스 및 견적 주입 통합 테스트에서를 테스트 하려면 테스트에서 모의 서비스는는 SUT에 주입 됩니다. 모의 서비스 응용 프로그램의 대체 `QuoteService` 테스트 앱에서 제공 하는 서비스를 사용 하 여 호출 `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` 호출 되 고 범위가 지정 된 서비스에 등록 됩니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

테스트의 실행 중에 생성 된 태그를 반영 하 여 제공 된 견적 텍스트 `TestQuoteService`, 따라서 어설션 전달 합니다.

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>테스트 인프라에서 앱 콘텐츠 루트 경로 유추 하는 방법

합니다 `WebApplicationFactory` 생성자에 대 한 검색 하 여 앱 콘텐츠 루트 경로 유추를 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) 같은 키를 사용 하 여 통합 테스트를 포함 하는 어셈블리에는 `TEntryPoint` 어셈블리`System.Reflection.Assembly.FullName`. 경우 올바른 키를 사용 하 여 특성을 찾을 수 없으면 `WebApplicationFactory` 대체 솔루션 파일을 검색 합니다 ( *\*.sln*) 추가 하 고는 `TEntryPoint` 솔루션 디렉터리에 어셈블리 이름입니다. 응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로) 뷰 및 콘텐츠 파일을 검색할 사용 됩니다.

대부분의 경우에서 필요는 없습니다 앱 콘텐츠 루트를 명시적으로 설정 검색 논리는 일반적으로 런타임 시 올바른 콘텐츠 루트를 찾으면 됩니다. 콘텐츠 루트는 없는 특별 한 시나리오에서 기본 제공 검색 알고리즘을 명시적으로 또는 사용자 지정 논리를 사용 하 여 루트를 지정할 수 있습니다 콘텐츠 앱을 사용 합니다. 이러한 시나리오에서 앱 콘텐츠 루트를 설정 하려면 호출을 `UseSolutionRelativeContentRoot` 에서 확장 메서드는 [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) 패키지 합니다. 솔루션의 상대 경로 및 선택적 솔루션 파일 이름 또는 와일드 카드 사용 패턴을 제공 (기본값 = `*.sln`).

호출 된 [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot) 확장 메서드를 사용 하 여 *하나* 다음 방법 중:

* 테스트 클래스를 구성할 때 `WebApplicationFactory`를 사용 하 여 사용자 지정 구성 정보를 제공 합니다 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* 사용자 지정을 사용 하 여 테스트 클래스를 구성할 때 `WebApplicationFactory`에서 상속 `WebApplicationFactory` 시키고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>섀도 복사를 사용 하지 않도록 설정

출력 디렉터리 보다 다른 디렉터리에서 실행할 테스트를 사용 하면 섀도 복사 합니다. 제대로 작동 하려면 테스트에 대 한 섀도 복사를 비활성화 해야 합니다. [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit을 사용 하 고 포함 하 여 xunit 섀도 복사를 사용 하지 않도록 설정 된 *xunit.runner.json* 올바른 구성 설정 사용 하 여 파일. 자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)합니다.

추가 된 *xunit.runner.json* 다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 파일:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>개체 삭제

테스트 후 합니다 `IClassFixture` 구현을 실행 됩니다 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient) xUnit 삭제 될 때 삭제 됩니다 합니다 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) . 개발자가 인스턴스화된 개체 삭제에 필요한 경우에 삭제 된 `IClassFixture` 구현 합니다. 자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)합니다.

## <a name="integration-tests-sample"></a>통합 테스트 샘플

합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 두 개의 앱으로 구성 됩니다.

| 앱 | 프로젝트 디렉터리 | Description |
| --- | ----------------- | ----------- |
| 메시지 앱 (SUT) | *src/RazorPagesProject* | 추가, 하나를 삭제, all, 삭제 및 메시지를 분석할 수 있습니다. |
| 테스트 앱 | *tests/RazorPagesProject.Tests* | 통합 테스트는 SUT 하는 데 사용 합니다. |

와 같은 기본 제공 테스트에 대 한 기능의 IDE 사용 하 여 테스트를 실행할 수 있습니다 [Visual Studio](https://visualstudio.microsoft.com)합니다. 사용 하는 경우 [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄에서 명령 프롬프트에서 다음 명령을 실행 합니다 *tests/RazorPagesProject.Tests* 디렉터리:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>메시지 앱 (SUT) 조직

SUT는 다음 특성을 사용 하 여 Razor 페이지 메시지 시스템:

* 앱의 인덱스 페이지 (*pages/Index.cshtml* 하 고 *Pages/Index.cshtml.cs*) UI 및 페이지 추가, 삭제 및 메시지 (메시지 당 평균 단어) 분석을 제어 하려면 모델 메서드 제공 .
* 메시지에서 설명 합니다 `Message` 클래스 (*Data/Message.cs*) 두 가지 속성을 사용 하 여: `Id` (키) 및 `Text` (메시지). `Text` 속성 필요 하 고 200 자로 제한 됩니다.
* 메시지를 사용 하 여 저장 됩니다 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;합니다.
* 해당 데이터베이스 컨텍스트 클래스의를 DAL (데이터 액세스 계층)를 포함 하는 앱 `AppDbContext` (*Data/AppDbContext.cs*).
* 데이터베이스 앱 시작 시 비어 있으면 메시지 저장소는 세 개의 메시지를 사용 하 여 초기화 됩니다.
* 앱 포함을 `/SecurePage` 인증 된 사용자만 액세스할 수 있는 합니다.

&#8224;EF 항목인 [inmemory 테스트](/ef/core/miscellaneous/testing/in-memory), MSTest 사용한 테스트에 대 한 메모리 내 데이터베이스를 사용 하는 방법에 설명 합니다. 이 항목에서는 사용 된 [xUnit](https://xunit.github.io/) 테스트 프레임 워크입니다. 테스트 개념 다른 테스트 프레임 워크에서 구현 테스트와 유사 하지만 동일 하지입니다.

앱 리포지토리 패턴을 사용 하지 않습니다 하 고 효과적인 예가 없습니다 있지만 합니다 [작업 단위 (UoW) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor 페이지는 이러한 패턴을 개발을 지원 합니다. 자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 하 고 [컨트롤러 논리 테스트](/aspnet/core/mvc/controllers/testing) (샘플 리포지토리 패턴을 구현 하는 데 사용).

### <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱 내에서 콘솔 앱은는 *tests/RazorPagesProject.Tests* 디렉터리입니다.

| 테스트 앱 디렉터리 | Description |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs* 라우팅, 인증 되지 않은 사용자, 보안 페이지에 액세스 하 고 GitHub 사용자 프로필 및 프로필의 사용자 로그인을 확인 하는 것에 대 한 테스트 메서드가 포함 되어 있습니다. |
| *IntegrationTests* | *IndexPageTests.cs* 사용자 지정을 사용 하 여 인덱스 페이지에 대 한 통합 테스트를 포함 `WebApplicationFactory` 클래스입니다. |
| *도우미/유틸리티* | <ul><li>*Utilities.cs* 포함 된 `InitializeDbForTests` 테스트 데이터로 데이터베이스 시드를 사용 하는 방법입니다.</li><li>*HtmlHelpers.cs* AngleSharp를 반환 하는 방법을 제공 `IHtmlDocument` 테스트 메서드를 사용 합니다.</li><li>*HttpClientExtensions.cs* 에 대 한 오버 로드를 제공 `SendAsync` 는 SUT에 요청을 제출 합니다.</li></ul> |

테스트 프레임 워크 [xUnit](https://xunit.github.io/)합니다. 통합 테스트를 사용 하 여 수행 되는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 포함 하는 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). 때문에 합니다 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트 및 테스트 서버를 구성 하는 데 사용 되는 `TestHost` 및 `TestServer` 패키지 테스트 앱의 프로젝트 파일에서 직접 패키지 참조가 필요 하지 않습니다 또는 테스트 앱의 개발자 구성입니다.

**테스트에 대 한 데이터베이스 시드**

통합 테스트는 테스트 실행 하기 전에 데이터베이스의 작은 데이터 집합을 일반적으로 필요합니다. 예를 들어 삭제 하므로 데이터베이스 delete 요청이 성공할 수에 대 한 레코드를 하나 이상 있어야 합니다. 데이터베이스 레코드 삭제에 대 한 호출을 테스트 합니다.

샘플 앱에서 세 개의 메시지를 사용 하 여 데이터베이스를 시드합니다 *Utilities.cs* 테스트를 실행할 때 사용할 수 있는:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>추가 자료

* [단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Razor 페이지 단위 테스트](xref:test/razor-pages-tests)
* [미들웨어](xref:fundamentals/middleware/index)
* [테스트 컨트롤러](xref:mvc/controllers/testing)
