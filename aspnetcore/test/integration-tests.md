---
title: ASP.NET Core의 통합 테스트
author: guardrex
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: test/integration-tests
ms.openlocfilehash: a86bf2b183a81f0b903a12f9d1660fb32faa6c03
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819935"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core의 통합 테스트

By [Luke Latham 문자](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com/)

통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다. ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.

이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다. 테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다. Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.

* [Razor 페이지 소개](xref:razor-pages/index)
* [Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 페이지 단위 테스트](xref:test/razor-pages-tests)

> [!NOTE]
> SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.

## <a name="introduction-to-integration-tests"></a>통합 테스트 소개

통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다. 단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다. 통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.

이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.

* 데이터베이스
* 파일 시스템
* 네트워크 어플라이언스
* 요청-응답 파이프라인

단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.

단위 테스트와 달리 통합 테스트는 다음과 같습니다.

* 프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.
* 더 많은 코드와 데이터 처리가 필요 합니다.
* 실행 하는 데 시간이 더 오래 걸립니다.

따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다. 단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.

> [!TIP]
> 데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요. 응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다. 이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다. 단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.

> [!NOTE]
> 통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 통합 테스트

ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.

* 테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다. 테스트 프로젝트에는 테스트 *대상 시스템* (sut) 이라고 하는 테스트 된 ASP.NET Core 프로젝트에 대 한 참조가 있습니다. _"SUT"는이 항목 전체에서 테스트 된 앱을 참조 하는 데 사용 됩니다._
* 테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT에 대 한 요청 및 응답을 처리 합니다.
* Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.

통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.

1. SUT의 웹 호스트가 구성 되어 있습니다.
1. 앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.
1. *정렬* 테스트 단계를 실행 합니다. 테스트 앱은 요청을 준비 합니다.
1. *Act* 테스트 단계를 실행 합니다. 클라이언트는 요청을 제출 하 고 응답을 받습니다.
1. *Assert* 테스트 단계가 실행 됩니다. *실제* 응답의 유효성은 *예상* 된 응답에 따라 *성공* 또는 *실패* 로 확인 됩니다.
1. 모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.
1. 테스트 결과가 보고 됩니다.

일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다. 예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.

테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다. 이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.

패키지 `Microsoft.AspNetCore.Mvc.Testing` 에서 처리 하는 작업은 다음과 같습니다.

* Sut의 종속성 파일 ( *\*. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.
* 테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 콘텐츠 루트를 SUT의 프로젝트 루트로 설정 합니다.
* `TestServer`를 사용하여 Sut를 간소화하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.

[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.

> [!NOTE]
> 앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다. 이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다. 단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.

Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다. 유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다. Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스 (예: `IndexPageTests` 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하는 데 사용할 수 있습니다) 뒤에 이름이 지정 됩니다. MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 `HomeControllerTests` 하는 컨트롤러 (예: 홈 컨트롤러에 대 한 구성 요소 통합 테스트) 뒤에 이름이 지정 됩니다.

## <a name="test-app-prerequisites"></a>응용 프로그램 테스트 필수 조건

테스트 프로젝트는 다음을 수행 해야 합니다.

* 다음 패키지를 참조 합니다.
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정 합니다. 웹 SDK는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요 합니다.

이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다. *테스트/RazorPagesProject* . 테스트/razor 프로젝트. .csproj 파일을 검사 합니다. 샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 환경

SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>기본 WebApplicationFactory를 사용 하는 기본 테스트

[WebApplicationFactory&lt;tentrypoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다. `TEntryPoint`는 sut의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.

테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.

### <a name="basic-test-of-app-endpoints"></a>앱 끝점의 기본 테스트

`BasicTests`다음 테스트 클래스인는 `WebApplicationFactory` 를 사용 하 여 sut를 부트스트랩 하 고 테스트 메서드 ( `Get_EndpointsReturnSuccessAndCorrectContentType`)에 [httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다. 메서드는 응답 상태 코드 (200-299 범위의 상태 코드)가 성공 했는지 확인 하 고 헤더는 `Content-Type` 여러 앱 페이지에 대 한 것 `text/html; charset=utf-8` 입니다.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 `HttpClient` 따르며 쿠키를 처리 하는의 인스턴스를 만듭니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다. TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다. 쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.

### <a name="test-a-secure-endpoint"></a>보안 끝점 테스트

`BasicTests` 클래스의 다른 테스트에서는 보안 끝점이 인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션하는 지 확인 합니다.

Sut에서 페이지는 `/SecurePage` [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다. 자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 은 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를로 `false`설정 하 여 리디렉션을 허용 하지 않도록 설정 됩니다. `Get_SecurePageRequiresAnAuthenticatedUser`

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.

* SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.
* 응답 헤더의 `http://localhost/Identity/Account/Login` `Location` 헤더 값은 헤더가 존재 하지 않는 최종 로그인 페이지 응답이 아니라로 시작 하는지 확인 하기 위해 확인 됩니다. `Location`

에 대 `WebApplicationFactoryClientOptions`한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 사용자 지정

웹 호스트 구성은에서 `WebApplicationFactory` 를 상속 하 여 하나 이상의 사용자 지정 팩터리를 만들기 때문에 테스트 클래스와 독립적으로 만들 수 있습니다.

1. 에서 `WebApplicationFactory` 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다. [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서 데이터베이스 시드는 `InitializeDbForTests` 메서드를 통해 수행 됩니다. 메서드는 [통합 테스트 샘플에 설명 되어 있습니다. 앱 구성](#test-app-organization) 섹션을 테스트 합니다.

2. 테스트 클래스에서 `CustomWebApplicationFactory` 사용자 지정을 사용 합니다. 다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   샘플 앱의 클라이언트는 다음 리디렉션이 발생 하지 않도록 `HttpClient` 구성 되어 있습니다. [보안 끝점 테스트](#test-a-secure-endpoint) 섹션에서 설명한 대로이를 통해 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다. 첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.

3. 일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다. 테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.

1. 페이지에 대 한 요청을 만듭니다.
1. 위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.
1. 위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.

`SendAsync`도우미 확장 메서드 (*도우미/httpclientextensions .cs*)와 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `GetDocumentAsync`도우미 메서드 (*도우미/htmlhelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 위조 방지를 처리 합니다. 다음 방법으로 확인 합니다.

* `GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다. `GetDocumentAsync`원본`HttpResponseMessage`에 기반 하 여 *가상 응답* 을 준비 하는 팩터리를 사용 합니다. 자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.
* `SendAsync``HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 및 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 작성 하는에 대 한 확장 메서드로 sut에 요청을 제출 합니다. 에 대 `SendAsync` 한 오버 로드는 HTML`IHtmlFormElement`폼 () 및 다음을 허용 합니다.
  * 양식의 제출 단추 (`IHtmlElement`)
  * Form values collection (`IEnumerable<KeyValuePair<string, string>>`)
  * 전송 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다. AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다. [HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다. 또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정

테스트 메서드 내에서 추가 구성이 필요한 경우 [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 `WebApplicationFactory` 지정 되는 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새를 만듭니다.

`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 테스트 메서드는를 사용 하는 `WithWebHostBuilder`방법을 보여 줍니다. 이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.

`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하는 작업을 수행 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행 될 수 있기 때문에이 테스트 메서드에서는 sut가 삭제할 레코드가 있는지 확인 하기 위해 데이터베이스를 시드 합니다. Sut에서 `messages` 폼의 단추를선택하는것은sut에대한요청에서시뮬레이션됩니다.`deleteBtn1`

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>클라이언트 옵션

다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)을 보여 줍니다.

| 옵션 | 설명 | 기본값 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` 인스턴스의 기본 주소를 가져오거나 설정 합니다. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 인스턴스에서 쿠키를 처리할지 `HttpClient` 여부를 가져오거나 설정 합니다. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 인스턴스가 따라야 하는 `HttpClient` 최대 리디렉션 응답 수를 가져오거나 설정 합니다. | 7 |

`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>모의 서비스 삽입

호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다. **모의 서비스를 삽입 하려면 sut에 `Startup` `Startup.ConfigureServices` 메서드를 포함 하는 클래스가 있어야 합니다.**

샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다. 인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index .cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다. 모의 서비스는 응용 프로그램 `QuoteService` `TestQuoteService`을 테스트 앱에서 제공 하는 서비스로 대체 합니다.

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`가 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

테스트 실행 중에 생성 된 태그는에서 `TestQuoteService`제공 하는 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법

`WebApplicationFactory`생성자는 `TEntryPoint`어셈블리`System.Reflection.Assembly.FullName` 와 동일한 키를 사용하여 통합테스트를 포함하는 어셈블리에서[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 콘텐츠 루트 경로를 유추합니다. 올바른 키를 가진 특성을 찾을 수 `WebApplicationFactory` 없는 경우에서 솔루션 파일 ( *\*.sln*)을 검색 하 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다. 응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.

## <a name="disable-shadow-copying"></a>섀도 복사 사용 안 함

섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다. 테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다. [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다. 자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.

다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>개체 삭제

`IClassFixture` 구현에 대 한 테스트를 실행 한 후 xunit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다. 개발자가 인스턴스화한 개체를 삭제 해야 하는 경우에는 `IClassFixture` 구현에서 삭제 합니다. 자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)합니다.

## <a name="integration-tests-sample"></a>통합 테스트 샘플

[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.

| 앱 | 프로젝트 디렉터리 | Description |
| --- | ----------------- | ----------- |
| 메시지 앱 (SUT) | *src/RazorPagesProject* | 사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다. |
| 응용 프로그램 테스트 | *tests/RazorPagesProject.Tests* | SUT 테스트를 통합 하는 데 사용 됩니다. |

[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>메시지 앱 (SUT) 조직

SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.
* 메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다. `Text` 속성은 필수 이며 200 자로 제한 됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.
* 앱에는 데이터베이스 컨텍스트 클래스 (*data/AppDbContext*)에 DAL (데이터 액세스 `AppDbContext` 계층)이 포함 되어 있습니다.
* 응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.
* 앱에는 인증 `/SecurePage` 된 사용자만 액세스할 수 있는가 포함 되어 있습니다.

&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다. 이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다. 여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.

앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다. 자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.

### <a name="test-app-organization"></a>앱 구성 테스트

테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.

| 응용 프로그램 디렉터리 테스트 | Description |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs* 에는 라우팅을 위한 테스트 메서드, 인증 되지 않은 사용자가 보안 페이지에 액세스 하는 방법, GitHub 사용자 프로필 가져오기 및 프로필의 사용자 로그인 확인이 포함 됩니다. |
| *IntegrationTests* | *IndexPageTests.cs* 에는 사용자 지정 `WebApplicationFactory` 클래스를 사용 하는 인덱스 페이지에 대 한 통합 테스트가 포함 되어 있습니다. |
| *도우미/유틸리티* | <ul><li>*Utilities.cs* 에는 `InitializeDbForTests` 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 메서드가 포함 되어 있습니다.</li><li>*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument` 을 반환 하는 메서드를 제공 합니다.</li><li>*HttpClientExtensions.cs* 는에 요청 `SendAsync` 을 전송 하는에 대 한 오버 로드를 제공 합니다.</li></ul> |

테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다. [AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다. [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버 `TestHost` 를 구성 하는 데 사용 되므로 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 개발자에 게 직접 패키지 참조가 필요 하지 않습니다. 테스트 응용 프로그램의 구성입니다.

**테스트를 위해 데이터베이스 시드**

통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다. 예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.

샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>추가 자료

* [단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Razor 페이지 단위 테스트](xref:test/razor-pages-tests)
* [미들웨어](xref:fundamentals/middleware/index)
* [테스트 컨트롤러](xref:mvc/controllers/testing)
