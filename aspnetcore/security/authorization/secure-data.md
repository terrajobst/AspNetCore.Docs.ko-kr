---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 Razor Pages 앱을 만드는 방법을 알아봅니다. HTTPS, 인증, 보안 ASP.NET Core Id를 포함 합니다.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 6e2f785a6dc014884f105766686f284cb2685530
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816155"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core MVC 버전은 [이 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 를 참조 하세요. 이 자습서의 ASP.NET Core 1.1 버전은 [이](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) 폴더에 있습니다. 1\.1 ASP.NET Core 샘플은 [샘플](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)에 있습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) 보기

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다. 사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다. 다음 세 가지 보안 그룹이 있습니다.

* **등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.
* **관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다. 승인 된 연락처만 사용자에 게 표시 됩니다.
* **관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.

이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.

다음 이미지에서는 사용자 Rick (`rick@example.com`)가 로그인 되어 있습니다. Rick는 승인 된 연락처만 볼 수 있고 **편집**/**삭제**/연락처에 대 한 새 링크를 **만들** 수 있습니다. Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다. 관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

다음 그림에서 `manager@contoso.com`는 관리자 역할에 로그인 되어 있습니다.

![로그인 manager@contoso.com를 보여 주는 스크린샷](secure-data/_static/manager1.png)

다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.

![연락처의 관리자 보기](secure-data/_static/manager.png)

**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.

다음 그림에서 `admin@contoso.com`는 관리자 역할에 로그인 되어 있습니다.

![로그인 admin@contoso.com를 보여 주는 스크린샷](secure-data/_static/admin.png)

관리자에 게는 모든 권한이 있습니다. 연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.

앱은 다음 `Contact` 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 만들어졌습니다.

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.

* `ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.
* `ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.
* `ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.

## <a name="prerequisites"></a>Prerequisites

이 자습서는 고급입니다. 다음에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [인증](xref:security/authentication/identity)
* [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [권한 부여](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>시작 및 완료 된 앱

[완성](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다. 보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.

### <a name="the-starter-app"></a>시작 앱

[시작](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.

앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.

## <a name="secure-user-data"></a>보안 사용자 데이터

다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다. 완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.

### <a name="tie-the-contact-data-to-the-user"></a>사용자에 게 연락처 데이터 연결

ASP.NET [id](xref:security/authentication/identity) 사용자 id를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다. `Contact` 모델에 `OwnerID` 및 `ContactStatus`를 추가 합니다.

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`는 [id](xref:security/authentication/identity) 데이터베이스의 `AspNetUser` 테이블에서 가져온 사용자의 id입니다. `Status` 필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.

새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id에 역할 서비스 추가

역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>인증 된 사용자 필요

사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 `[AllowAnonymous]` 특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다. 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 하면 새로 추가 된 Razor Pages 및 컨트롤러가 보호 됩니다. 기본적으로 인증을 요구 하는 것은 새 컨트롤러에 의존 하는 것 보다 더 안전 하며, Razor Pages `[Authorize]` 특성을 포함 합니다.

익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스 및 개인 정보 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>테스트 계정 구성

`SeedData` 클래스는 관리자와 관리자 라는 두 개의 계정을 만듭니다. 암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다. 프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

강력한 암호를 지정 하지 않으면 `SeedData.Initialize`를 호출할 때 예외가 throw 됩니다.

테스트 암호를 사용 하도록 `Main`를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>테스트 계정을 만들고 연락처를 업데이트 합니다.

`SeedData` 클래스에서 `Initialize` 메서드를 업데이트 하 여 테스트 계정을 만듭니다.

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

관리자 사용자 ID를 추가 하 고 연락처에 `ContactStatus` 합니다. 연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다. 모든 연락처에 사용자 ID 및 상태를 추가 합니다. 하나의 연락처만 표시 됩니다.

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>소유자, 관리자 및 관리자 권한 부여 처리기 만들기

*권한 부여* 폴더에 `ContactIsOwnerAuthorizationHandler` 클래스를 만듭니다. `ContactIsOwnerAuthorizationHandler`는 리소스에서 사용 하는 사용자가 리소스를 소유 하 고 있는지 확인 합니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` 컨텍스트를 호출 합니다 [. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다. 권한 부여 처리기 일반적:

* 요구 사항이 충족 되 면 `context.Succeed`을 반환 합니다.
* 요구 사항이 충족 되지 않은 경우 `Task.CompletedTask`을 반환 합니다. `Task.CompletedTask`는 성공 또는 실패&mdash;다른 권한 부여 처리기를 실행할 수 있습니다.

명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다. `ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.

### <a name="create-a-manager-authorization-handler"></a>관리자 권한 부여 처리기 만들기

*권한 부여* 폴더에 `ContactManagerAuthorizationHandler` 클래스를 만듭니다. `ContactManagerAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다. 관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>관리자 권한 부여 처리기 만들기

*권한 부여* 폴더에 `ContactAdministratorsAuthorizationHandler` 클래스를 만듭니다. `ContactAdministratorsAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다. 관리자는 모든 작업을 수행할 수 있습니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>권한 부여 처리기 등록

[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다. `ContactIsOwnerAuthorizationHandler`은 Entity Framework Core을 기반으로 하는 ASP.NET Core [id](xref:security/authentication/identity)를 사용 합니다. [종속성 주입](xref:fundamentals/dependency-injection)을 통해 `ContactsController`에서 사용할 수 있도록 서비스 컬렉션에 처리기를 등록 합니다. `ConfigureServices`의 끝에 다음 코드를 추가 합니다.

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목로 추가 됩니다. 단일 항목는 EF를 사용 하지 않고 필요한 모든 정보는 `HandleRequirementAsync` 메서드의 `Context` 매개 변수에 있기 때문에 더 이상 사용 되지 않습니다.

## <a name="support-authorization"></a>인증 지원

이 섹션에서는 Razor Pages를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.

### <a name="review-the-contact-operations-requirements-class"></a>연락처 작업 요구 사항 클래스를 검토 합니다.

`ContactOperations` 클래스를 검토 합니다. 이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>연락처에 대 한 기본 클래스를 만듭니다 Razor Pages

연락처 Razor Pages에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다. 기본 클래스는 초기화 코드를 한 위치에 배치 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

위의 코드는:

* 권한 부여 처리기에 액세스 하는 `IAuthorizationService` 서비스를 추가 합니다.
* Id `UserManager` 서비스를 추가 합니다.
* `ApplicationDbContext`를 추가합니다.

### <a name="update-the-createmodel"></a>CreateModel 업데이트

`DI_BasePageModel` 기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` 메서드를 다음으로 업데이트 합니다.

* 사용자 ID를 `Contact` 모델에 추가 합니다.
* 권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>IndexModel 업데이트

승인 된 연락처만 일반 사용자에 게 표시 되도록 `OnGetAsync` 메서드를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel 업데이트

사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다. 리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다. 특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다. 리소스 기반 권한 부여는 필수적 이어야 합니다. 앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다. 리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel 업데이트

권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>권한 부여 서비스를 뷰에 삽입 합니다.

현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.

모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

위의 태그는 여러 개의 `using` 문을 추가 합니다.

적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다. 링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다. 사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다. Razor 페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.

### <a name="update-details"></a>업데이트 세부 정보

관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

세부 정보 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>역할에 사용자 추가 또는 제거

에 대 한 자세한 내용은 다음 [문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.

* 사용자가 권한을 제거 합니다. 예를 들어 채팅 앱에서 사용자를 음소거 합니다.
* 사용자에 게 권한 추가

## <a name="differences-between-challenge-vs-forbid"></a>챌린지 vs 금지 간의 차이점

이 앱은 인증 된 사용자를 [요구](#require-authenticated-users)하도록 기본 정책을 설정 합니다. 다음 코드는 익명 사용자를 허용 합니다. 익명 사용자는 챌린지 vs 금지 간의 차이점을 표시할 수 있습니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

위의 코드에서

* 사용자가 인증 **되지 않은** 경우 `ChallengeResult` 반환 됩니다. `ChallengeResult` 반환 되 면 사용자가 로그인 페이지로 리디렉션됩니다.
* 사용자가 인증 되었지만 권한이 부여 되지 않은 경우 `ForbidResult` 반환 됩니다. `ForbidResult` 반환 되 면 사용자는 액세스 거부 페이지로 리디렉션됩니다.

## <a name="test-the-completed-app"></a>완성 된 앱 테스트

시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.

* 강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다. 예를 들어 `Passw0rd!`은 강력한 암호 요구 사항을 충족 합니다.
* 프로젝트의 폴더에서 다음 명령을 실행 합니다. 여기서 `<PW>`는 암호입니다.

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

앱에 연락처가 있는 경우:

* `Contact` 테이블의 모든 레코드를 삭제 합니다.
* 응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.

완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다. 한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com`). 다른 사용자로 각 브라우저에 로그인 합니다. 다음 작업을 확인 합니다.

* 등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.
* 관리자는 연락처 데이터를 승인/거부할 수 있습니다. `Details` 보기에는 **승인** 및 **거부** 단추가 표시 됩니다.
* 관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.

| 사용자                | 앱에서 시드 | 옵션                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 아니요                | 자신의 데이터를 편집/삭제 합니다.                |
| manager@contoso.com | 예               | 자신의 데이터를 승인/거부 하 고 편집/삭제 합니다. |
| admin@contoso.com   | 예               | 모든 데이터를 승인/거부 하 고 편집/삭제 합니다. |

관리자의 브라우저에서 연락처를 만듭니다. 관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다. 이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.

## <a name="create-the-starter-app"></a>시작 앱 만들기

* "연락처 관리자" 라는 Razor Pages 앱 만들기
  * **개별 사용자 계정을**사용 하 여 앱을 만듭니다.
  * 네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.
  * `-uld`는 SQLite 대신 LocalDB를 지정 합니다.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *모델/연락처를 추가 합니다. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` 모델을 스 캐 폴드 합니다.
* 초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

`dotnet aspnet-codegenerator razorpage` 명령을 사용 하 여 버그를 발생 하는 경우 [이 GitHub 문제](https://github.com/aspnet/Scaffolding/issues/984)를 참조 하세요.

* *Pages/Shared/Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* 연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.

### <a name="seed-the-database"></a>데이터베이스 시드

*데이터* 폴더에 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

`Main`에서 `SeedData.Initialize`를 호출 합니다.

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다. Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다. 사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다. 다음 세 가지 보안 그룹이 있습니다.

* **등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.
* **관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다. 승인 된 연락처만 사용자에 게 표시 됩니다.
* **관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.

다음 이미지에서는 사용자 Rick (`rick@example.com`)가 로그인 되어 있습니다. Rick는 승인 된 연락처만 볼 수 있고 **편집**/**삭제**/연락처에 대 한 새 링크를 **만들** 수 있습니다. Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다. 관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

다음 그림에서 `manager@contoso.com`는 관리자 역할에 로그인 되어 있습니다.

![로그인 manager@contoso.com를 보여 주는 스크린샷](secure-data/_static/manager1.png)

다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.

![연락처의 관리자 보기](secure-data/_static/manager.png)

**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.

다음 그림에서 `admin@contoso.com`는 관리자 역할에 로그인 되어 있습니다.

![로그인 admin@contoso.com를 보여 주는 스크린샷](secure-data/_static/admin.png)

관리자에 게는 모든 권한이 있습니다. 연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.

앱은 다음 `Contact` 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 만들어졌습니다.

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.

* `ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.
* `ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.
* `ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.

## <a name="prerequisites"></a>Prerequisites

이 자습서는 고급입니다. 다음에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [인증](xref:security/authentication/identity)
* [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [권한 부여](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>시작 및 완료 된 앱

[완성](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다. 보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.

### <a name="the-starter-app"></a>시작 앱

[시작](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.

앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.

## <a name="secure-user-data"></a>보안 사용자 데이터

다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다. 완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.

### <a name="tie-the-contact-data-to-the-user"></a>사용자에 게 연락처 데이터 연결

ASP.NET [id](xref:security/authentication/identity) 사용자 id를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다. `Contact` 모델에 `OwnerID` 및 `ContactStatus`를 추가 합니다.

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`는 [id](xref:security/authentication/identity) 데이터베이스의 `AspNetUser` 테이블에서 가져온 사용자의 id입니다. `Status` 필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.

새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id에 역할 서비스 추가

역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>인증 된 사용자 필요

사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 `[AllowAnonymous]` 특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다. 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 하면 새로 추가 된 Razor Pages 및 컨트롤러가 보호 됩니다. 기본적으로 인증을 요구 하는 것은 새 컨트롤러에 의존 하는 것 보다 더 안전 하며, Razor Pages `[Authorize]` 특성을 포함 합니다.

익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스, 정보 및 연락처 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>테스트 계정 구성

`SeedData` 클래스는 관리자와 관리자 라는 두 개의 계정을 만듭니다. 암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다. 프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

강력한 암호를 지정 하지 않으면 `SeedData.Initialize`를 호출할 때 예외가 throw 됩니다.

테스트 암호를 사용 하도록 `Main`를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>테스트 계정을 만들고 연락처를 업데이트 합니다.

`SeedData` 클래스에서 `Initialize` 메서드를 업데이트 하 여 테스트 계정을 만듭니다.

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

관리자 사용자 ID를 추가 하 고 연락처에 `ContactStatus` 합니다. 연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다. 모든 연락처에 사용자 ID 및 상태를 추가 합니다. 하나의 연락처만 표시 됩니다.

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>소유자, 관리자 및 관리자 권한 부여 처리기 만들기

*권한 부여* 폴더를 만들고 여기에 `ContactIsOwnerAuthorizationHandler` 클래스를 만듭니다. `ContactIsOwnerAuthorizationHandler`는 리소스에서 사용 하는 사용자가 리소스를 소유 하 고 있는지 확인 합니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` 컨텍스트를 호출 합니다 [. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다. 권한 부여 처리기 일반적:

* 요구 사항이 충족 되 면 `context.Succeed`을 반환 합니다.
* 요구 사항이 충족 되지 않은 경우 `Task.CompletedTask`을 반환 합니다. `Task.CompletedTask`는 성공 또는 실패&mdash;다른 권한 부여 처리기를 실행할 수 있습니다.

명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다. `ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.

### <a name="create-a-manager-authorization-handler"></a>관리자 권한 부여 처리기 만들기

*권한 부여* 폴더에 `ContactManagerAuthorizationHandler` 클래스를 만듭니다. `ContactManagerAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다. 관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>관리자 권한 부여 처리기 만들기

*권한 부여* 폴더에 `ContactAdministratorsAuthorizationHandler` 클래스를 만듭니다. `ContactAdministratorsAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다. 관리자는 모든 작업을 수행할 수 있습니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>권한 부여 처리기 등록

[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다. `ContactIsOwnerAuthorizationHandler`은 Entity Framework Core을 기반으로 하는 ASP.NET Core [id](xref:security/authentication/identity)를 사용 합니다. [종속성 주입](xref:fundamentals/dependency-injection)을 통해 `ContactsController`에서 사용할 수 있도록 서비스 컬렉션에 처리기를 등록 합니다. `ConfigureServices`의 끝에 다음 코드를 추가 합니다.

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목로 추가 됩니다. 단일 항목는 EF를 사용 하지 않고 필요한 모든 정보는 `HandleRequirementAsync` 메서드의 `Context` 매개 변수에 있기 때문에 더 이상 사용 되지 않습니다.

## <a name="support-authorization"></a>인증 지원

이 섹션에서는 Razor Pages를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.

### <a name="review-the-contact-operations-requirements-class"></a>연락처 작업 요구 사항 클래스를 검토 합니다.

`ContactOperations` 클래스를 검토 합니다. 이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>연락처에 대 한 기본 클래스를 만듭니다 Razor Pages

연락처 Razor Pages에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다. 기본 클래스는 초기화 코드를 한 위치에 배치 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

위의 코드는:

* 권한 부여 처리기에 액세스 하는 `IAuthorizationService` 서비스를 추가 합니다.
* Id `UserManager` 서비스를 추가 합니다.
* `ApplicationDbContext`를 추가합니다.

### <a name="update-the-createmodel"></a>CreateModel 업데이트

`DI_BasePageModel` 기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` 메서드를 다음으로 업데이트 합니다.

* 사용자 ID를 `Contact` 모델에 추가 합니다.
* 권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>IndexModel 업데이트

승인 된 연락처만 일반 사용자에 게 표시 되도록 `OnGetAsync` 메서드를 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel 업데이트

사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다. 리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다. 특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다. 리소스 기반 권한 부여는 필수적 이어야 합니다. 앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다. 리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel 업데이트

권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>권한 부여 서비스를 뷰에 삽입 합니다.

현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.

모든 보기에서 사용할 수 있도록 *views/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

위의 태그는 여러 개의 `using` 문을 추가 합니다.

적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다. 링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다. 사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다. Razor 페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.

### <a name="update-details"></a>업데이트 세부 정보

관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

세부 정보 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>역할에 사용자 추가 또는 제거

에 대 한 자세한 내용은 다음 [문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.

* 사용자가 권한을 제거 합니다. 예를 들어 채팅 앱에서 사용자를 음소거 합니다.
* 사용자에 게 권한 추가

## <a name="test-the-completed-app"></a>완성 된 앱 테스트

시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.

* 강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다. 예를 들어 `Passw0rd!`은 강력한 암호 요구 사항을 충족 합니다.
* 프로젝트의 폴더에서 다음 명령을 실행 합니다. 여기서 `<PW>`는 암호입니다.

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* 데이터베이스 삭제 및 업데이트

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* 응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.

완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다. 한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com`). 다른 사용자로 각 브라우저에 로그인 합니다. 다음 작업을 확인 합니다.

* 등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.
* 관리자는 연락처 데이터를 승인/거부할 수 있습니다. `Details` 보기에는 **승인** 및 **거부** 단추가 표시 됩니다.
* 관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.

| 사용자                | 앱에서 시드 | 옵션                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 아니요                | 자신의 데이터를 편집/삭제 합니다.                |
| manager@contoso.com | 예               | 자신의 데이터를 승인/거부 하 고 편집/삭제 합니다. |
| admin@contoso.com   | 예               | 모든 데이터를 승인/거부 하 고 편집/삭제 합니다. |

관리자의 브라우저에서 연락처를 만듭니다. 관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다. 이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.

## <a name="create-the-starter-app"></a>시작 앱 만들기

* "연락처 관리자" 라는 Razor Pages 앱 만들기
  * **개별 사용자 계정을**사용 하 여 앱을 만듭니다.
  * 네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.
  * `-uld`는 SQLite 대신 LocalDB를 지정 합니다.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *모델/연락처를 추가 합니다. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` 모델을 스 캐 폴드 합니다.
* 초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* *Pages/Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.

### <a name="seed-the-database"></a>데이터베이스 시드

*데이터* 폴더에 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스를 추가 합니다.

`Main`에서 `SeedData.Initialize`를 호출 합니다.

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다. Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>추가 자료

* [Azure App Service에서 .NET Core 및 SQL Database 웹 앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [권한 부여 랩을 ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다. 이 랩에서는이 자습서에서 소개 하는 보안 기능에 대해 자세히 설명 합니다.
* <xref:security/authorization/introduction>
* [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies)
