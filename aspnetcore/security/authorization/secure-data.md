---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 Razor 페이지 앱을 만드는 방법에 알아봅니다. HTTPS, 인증, 보안, ASP.NET Core Id를 포함합니다.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: d95f44394d6ecc3c3896b45c5bebc73fa2d92445
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011192"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

참조 [이 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC 버전에 대 한 합니다. 이 자습서에서는 ASP.NET Core 1.1 버전은 [이](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) 폴더입니다. ASP.NET Core 샘플에는 1.1 합니다 [샘플](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

참조 [이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다. 인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다. 세 가지 보안 그룹이 있습니다.

* **등록 된 사용자** 편집/삭제할 수 있습니다 자신의 데이터 및 승인 된 모든 데이터를 볼 수 있습니다.
* **관리자** 승인 하거나 연락처 데이터를 거부할 수 있습니다. 승인 된 연락처만 사용자에 게 표시 됩니다.
* **관리자** 승인/거부를 편집/삭제할 모든 데이터입니다.

이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.

다음 이미지에서는 Rick 사용자 (`rick@example.com`)에 로그인 됩니다. Rick 승인 된 연락처를 보기만 할 수 있습니다 하 고 **편집할**/**삭제**/**새로 만들기** 자신의 연락처에 대 한 링크입니다. Rick을 표시 하 여 만든 마지막 레코드만 **편집** 하 고 **삭제** 링크 합니다. 다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

다음 이미지 `manager@contoso.com` 에서는 관리자 역할에 로그인 되어 있습니다.

![보여 주는 스크린샷 manager@contoso.com 로그인](secure-data/_static/manager1.png)

다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.

![연락처의 관리자의 보기](secure-data/_static/manager.png)

합니다 **승인** 하 고 **거부** 단추가 관리자와 관리자만 표시 됩니다.

다음 이미지 `admin@contoso.com` 에서는 및 관리자 역할에 로그인 되어 있습니다.

![보여 주는 스크린샷 admin@contoso.com 로그인](secure-data/_static/admin.png)

관리자는 모든 권한을 갖습니다. 그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.

앱에서 만든 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 다음 `Contact` 모델:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

다음 인증 처리기를 포함 하는 샘플:

* `ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.
* `ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.
* `ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 자습서 고급 옵션입니다. 에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [인증](xref:security/authentication/identity)
* [계정 확인 및 비밀번호 복구](xref:security/authentication/accconfirm)
* [권한 부여](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>시작 및 완료 된 앱

[다운로드](xref:index#how-to-download-a-sample) 는 [완료](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 앱. [테스트](#test-the-completed-app) 완성된 된 앱의 보안 기능에 익숙해질 수 있도록 합니다.

### <a name="the-starter-app"></a>시작 앱

[다운로드](xref:index#how-to-download-a-sample) 는 [스타터](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱.

앱 실행을 탭 합니다 **ContactManager** 에 연결 하 고 만들기, 편집 및 연락처를 삭제를 확인 합니다.

## <a name="secure-user-data"></a>사용자 데이터 보호

다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다. 완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.

### <a name="tie-the-contact-data-to-the-user"></a>사용자에 게 연락 데이터를 연결 합니다.

ASP.NET을 사용 하 여 [Identity](xref:security/authentication/identity) 데이터에 있지만 다른 사용자 데이터가 아닌 사용자 ID 사용자를 편집할 수 있습니다. 추가 `OwnerID` 하 고 `ContactStatus` 에 `Contact` 모델:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 사용자의 id를 `AspNetUser` 테이블에 [Identity](xref:security/authentication/identity) 데이터베이스입니다. `Status` 필드 연락처를 일반 사용자가 볼 수 있는지 여부를 결정 합니다.

새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id에 역할 서비스 추가

추가 [역할](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 역할 서비스를 추가 하려면:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>인증 된 사용자가 필요 합니다.

사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃할 수 있습니다는 `[AllowAnonymous]` 특성입니다. 새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다. 기본적으로 필요한 인증은 새 컨트롤러 및 포함할 Razor 페이지에 의존 하는 것 보다 안전 하지는 `[Authorize]` 특성입니다.

익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스 및 개인 정보 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>테스트 계정 구성

`SeedData` 클래스에는 두 개의 계정을 만듭니다: 관리자 및 관리자입니다. 사용 된 [암호 관리자 도구](xref:security/app-secrets) 이러한 계정에 대 한 암호를 설정 합니다. 프로젝트 디렉터리에서 암호를 설정 (포함 하는 디렉터리 *Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

강력한 암호를 지정 하지 않으면 예외가 발생 될 때 `SeedData.Initialize` 라고 합니다.

업데이트 `Main` 테스트 암호를 사용 합니다.

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>테스트 계정을 만들고 연락처를 업데이트 합니다.

업데이트를 `Initialize` 의 메서드는 `SeedData` 클래스 테스트 계정을 만들려면:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

관리자 사용자 ID를 추가 하 고 `ContactStatus` 연락처를 합니다. "제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다. 사용자 ID 및 상태를 모든 연락처를 추가 합니다. 하나만 표시 됩니다.

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>소유자, 관리자 및 관리자 권한 부여 처리기 만들기

만들기는 `ContactIsOwnerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다. `ContactIsOwnerAuthorizationHandler` 리소스에 역할을 하는 사용자 리소스를 소유 하는지 확인 합니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

합니다 `ContactIsOwnerAuthorizationHandler` 호출 [컨텍스트. 성공](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 현재 인증 된 사용자가 연락처 소유자입니다. 권한 부여 처리기 일반적으로:

* 반환 `context.Succeed` 요구 사항이 충족 되는 경우.
* 반환 `Task.CompletedTask` 요구 사항이 충족 되지 않는 경우. `Task.CompletedTask`은 (는) 성공&mdash;또는 실패 하지 않으므로 다른 권한 부여 처리기를 실행할 수 있습니다.

명시적으로 실패 하는 경우 반환 [컨텍스트. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)합니다.

앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다. `ContactIsOwnerAuthorizationHandler` 요구 사항 매개 변수에 전달 된 작업을 확인 하려면 필요 하지 않습니다.

### <a name="create-a-manager-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactManagerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다. `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다. 관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactAdministratorsAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다. `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다. 관리자는 모든 작업을 수행할 수 있습니다.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>인증 처리기를 등록 합니다.

Entity Framework Core를 사용 하 여 서비스에 등록 해야 합니다 [종속성 주입](xref:fundamentals/dependency-injection) 사용 하 여 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)합니다. 합니다 `ContactIsOwnerAuthorizationHandler` ASP.NET Core를 사용 하 여 [Identity](xref:security/authentication/identity), Entity Framework Core에서 빌드되는 합니다. 사용할 수 있도록 서비스 컬렉션을 사용 하 여 처리기를 등록 합니다 `ContactsController` 를 통해 [종속성 주입](xref:fundamentals/dependency-injection)합니다. 끝에 다음 코드를 추가 `ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목으로 추가 됩니다. EF를 사용 하지 않는 서 필요한 모든 정보는 단일 항목 들은 합니다 `Context` 의 매개 변수는 `HandleRequirementAsync` 메서드.

## <a name="support-authorization"></a>권한 부여를 지원 합니다.

이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.

### <a name="review-the-contact-operations-requirements-class"></a>연락처 작업 요구 사항 클래스를 검토 합니다.

검토를 `ContactOperations` 클래스입니다. 이 클래스는 포함 요구 사항을 지 원하는:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>연락처 Razor 페이지에 대 한 기본 클래스 만들기

연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다. 초기화 코드를 한 위치에 배치 하는 기본 클래스:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

위의 코드는:

* 추가 된 `IAuthorizationService` 권한 부여 처리기에 액세스 하는 서비스입니다.
* Id 추가 `UserManager` 서비스입니다.
* `ApplicationDbContext`를 추가합니다.

### <a name="update-the-createmodel"></a>업데이트 된 CreateModel

만들기 페이지 모델 생성자를 사용 하 여 업데이트를 `DI_BasePageModel` 기본 클래스:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

업데이트 된 `CreateModel.OnPostAsync` 방법:

* 사용자 ID를 추가 합니다 `Contact` 모델입니다.
* 사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>업데이트 된 IndexModel

업데이트 된 `OnGetAsync` 메서드 승인 된 연락처만 일반 사용자에 게 표시 됩니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>업데이트 된 EditModel

사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다. 리소스 권한 부여가 유효성을 검사 하기 때문에 `[Authorize]` 특성 충분 하지 않습니다. 특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다. 명령적 리소스 기반 권한 부여를 사용 해야 합니다. 페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다. 자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>업데이트 된 DeleteModel

인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>권한 부여 서비스 보기 삽입

현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.

모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

위의 태그는 몇 개 추가 `using` 문입니다.

업데이트를 **편집** 및 **삭제** 에 연결 *Pages/Contacts/Index.cshtml* 하므로 적절 한 권한 가진 사용자만 렌더링 됩니다.

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다. 링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다. 사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다. Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.

### <a name="update-details"></a>세부 정보 업데이트

관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

업데이트 세부 정보 페이지 모델:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>추가 하거나 역할에 사용자를 제거 합니다.

참조 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 에 대 한 내용은:

* 사용자의 권한을 제거 합니다. 예를 들어 채팅 앱에서 사용자를 음소거 합니다.
* 사용자에 권한을 추가 합니다.

## <a name="test-the-completed-app"></a>완성된 된 앱 테스트

시드 된 사용자 계정의 암호를 이미 설정 하지 않은 경우 사용 합니다 [암호 관리자 도구](xref:security/app-secrets#secret-manager) 암호를 설정 하려면:

* 강력한 암호를 선택 합니다. 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다. 예를 들어 `Passw0rd!` 강력한 암호 요구 사항을 충족 합니다.
* 프로젝트의 폴더에서 다음 명령을 실행 하는 `<PW>` 암호입니다.

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

앱에 있는 경우 연락처:

* 모든 레코드를 삭제 합니다 `Contact` 테이블입니다.
* 데이터베이스를 시드하려면 앱을 다시 시작 합니다.

완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다. 하나의 브라우저에서 새 사용자를 등록 합니다 (예를 들어 `test@example.com`). 다른 사용자와 각 브라우저에 로그인 합니다. 다음 작업을 확인 합니다.

* 등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.
* 관리자 수 승인/거부할 연락처 데이터입니다. 합니다 `Details` 표시를 볼 **승인** 하 고 **거부** 단추입니다.
* 관리자 승인/거부를 편집/삭제할 모든 데이터입니다.

| 사용자                | 앱에서 시드 | 옵션                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 아니요                | 자체 데이터를 편집/삭제 합니다.                |
| manager@contoso.com | 예               | 승인/거부 하 고 편집/삭제할 데이터를 소유 합니다. |
| admin@contoso.com   | 예               | 승인/거부 하 고 모든 데이터를 편집/삭제 합니다. |

관리자의 브라우저에서 연락처를 만듭니다. 삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다. 테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.

## <a name="create-the-starter-app"></a>시작 앱 만들기

* "ContactManager" 라는 Razor 페이지 앱 만들기
  * 응용 프로그램을 만들 **개별 사용자 계정**합니다.
  * 네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.
  * `-uld` SQLite 대신 LocalDB를 지정합니다.

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *모델/연락처를 추가 합니다. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* 스 캐 폴드는 `Contact` 모델입니다.
* 초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```console
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

* 만들기, 편집 및 연락처를 삭제 하 여 앱 테스트

### <a name="seed-the-database"></a>데이터베이스 시드

*데이터* 폴더에 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

호출 `SeedData.Initialize` 에서 `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

응용 프로그램 데이터베이스를 시드를 테스트 합니다. DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다. 인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다. 세 가지 보안 그룹이 있습니다.

* **등록 된 사용자** 편집/삭제할 수 있습니다 자신의 데이터 및 승인 된 모든 데이터를 볼 수 있습니다.
* **관리자** 승인 하거나 연락처 데이터를 거부할 수 있습니다. 승인 된 연락처만 사용자에 게 표시 됩니다.
* **관리자** 승인/거부를 편집/삭제할 모든 데이터입니다.

다음 이미지에서는 Rick 사용자 (`rick@example.com`)에 로그인 됩니다. Rick 승인 된 연락처를 보기만 할 수 있습니다 하 고 **편집할**/**삭제**/**새로 만들기** 자신의 연락처에 대 한 링크입니다. Rick을 표시 하 여 만든 마지막 레코드만 **편집** 하 고 **삭제** 링크 합니다. 다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

다음 이미지 `manager@contoso.com` 에서는 관리자 역할에 로그인 되어 있습니다.

![보여 주는 스크린샷 manager@contoso.com 로그인](secure-data/_static/manager1.png)

다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.

![연락처의 관리자의 보기](secure-data/_static/manager.png)

합니다 **승인** 하 고 **거부** 단추가 관리자와 관리자만 표시 됩니다.

다음 이미지 `admin@contoso.com` 에서는 및 관리자 역할에 로그인 되어 있습니다.

![보여 주는 스크린샷 admin@contoso.com 로그인](secure-data/_static/admin.png)

관리자는 모든 권한을 갖습니다. 그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.

앱에서 만든 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 다음 `Contact` 모델:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

다음 인증 처리기를 포함 하는 샘플:

* `ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.
* `ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.
* `ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서 고급 옵션입니다. 에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [인증](xref:security/authentication/identity)
* [계정 확인 및 비밀번호 복구](xref:security/authentication/accconfirm)
* [권한 부여](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>시작 및 완료 된 앱

[다운로드](xref:index#how-to-download-a-sample) 는 [완료](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 앱. [테스트](#test-the-completed-app) 완성된 된 앱의 보안 기능에 익숙해질 수 있도록 합니다.

### <a name="the-starter-app"></a>시작 앱

[다운로드](xref:index#how-to-download-a-sample) 는 [스타터](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱.

앱 실행을 탭 합니다 **ContactManager** 에 연결 하 고 만들기, 편집 및 연락처를 삭제를 확인 합니다.

## <a name="secure-user-data"></a>사용자 데이터 보호

다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다. 완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.

### <a name="tie-the-contact-data-to-the-user"></a>사용자에 게 연락 데이터를 연결 합니다.

ASP.NET을 사용 하 여 [Identity](xref:security/authentication/identity) 데이터에 있지만 다른 사용자 데이터가 아닌 사용자 ID 사용자를 편집할 수 있습니다. 추가 `OwnerID` 하 고 `ContactStatus` 에 `Contact` 모델:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 사용자의 id를 `AspNetUser` 테이블에 [Identity](xref:security/authentication/identity) 데이터베이스입니다. `Status` 필드 연락처를 일반 사용자가 볼 수 있는지 여부를 결정 합니다.

새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id에 역할 서비스 추가

추가 [역할](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 역할 서비스를 추가 하려면:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>인증 된 사용자가 필요 합니다.

사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃할 수 있습니다는 `[AllowAnonymous]` 특성입니다. 새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다. 기본적으로 필요한 인증은 새 컨트롤러 및 포함할 Razor 페이지에 의존 하는 것 보다 안전 하지는 `[Authorize]` 특성입니다.

추가 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 인덱스에, 정보 및 연락처 페이지 익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>테스트 계정 구성

`SeedData` 클래스에는 두 개의 계정을 만듭니다: 관리자 및 관리자입니다. 사용 된 [암호 관리자 도구](xref:security/app-secrets) 이러한 계정에 대 한 암호를 설정 합니다. 프로젝트 디렉터리에서 암호를 설정 (포함 하는 디렉터리 *Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

강력한 암호를 지정 하지 않으면 예외가 발생 될 때 `SeedData.Initialize` 라고 합니다.

업데이트 `Main` 테스트 암호를 사용 합니다.

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>테스트 계정을 만들고 연락처를 업데이트 합니다.

업데이트를 `Initialize` 의 메서드는 `SeedData` 클래스 테스트 계정을 만들려면:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

관리자 사용자 ID를 추가 하 고 `ContactStatus` 연락처를 합니다. "제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다. 사용자 ID 및 상태를 모든 연락처를 추가 합니다. 하나만 표시 됩니다.

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>소유자, 관리자 및 관리자 권한 부여 처리기 만들기

*권한 부여* 폴더를 만들고 여기에 `ContactIsOwnerAuthorizationHandler` 클래스를 만듭니다. `ContactIsOwnerAuthorizationHandler` 리소스에 역할을 하는 사용자 리소스를 소유 하는지 확인 합니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

합니다 `ContactIsOwnerAuthorizationHandler` 호출 [컨텍스트. 성공](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 현재 인증 된 사용자가 연락처 소유자입니다. 권한 부여 처리기 일반적으로:

* 반환 `context.Succeed` 요구 사항이 충족 되는 경우.
* 반환 `Task.CompletedTask` 요구 사항이 충족 되지 않는 경우. `Task.CompletedTask`은 (는) 성공&mdash;또는 실패 하지 않으므로 다른 권한 부여 처리기를 실행할 수 있습니다.

명시적으로 실패 하는 경우 반환 [컨텍스트. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)합니다.

앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다. `ContactIsOwnerAuthorizationHandler` 요구 사항 매개 변수에 전달 된 작업을 확인 하려면 필요 하지 않습니다.

### <a name="create-a-manager-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactManagerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다. `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다. 관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactAdministratorsAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다. `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다. 관리자는 모든 작업을 수행할 수 있습니다.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>인증 처리기를 등록 합니다.

Entity Framework Core를 사용 하 여 서비스에 등록 해야 합니다 [종속성 주입](xref:fundamentals/dependency-injection) 사용 하 여 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)합니다. 합니다 `ContactIsOwnerAuthorizationHandler` ASP.NET Core를 사용 하 여 [Identity](xref:security/authentication/identity), Entity Framework Core에서 빌드되는 합니다. 사용할 수 있도록 서비스 컬렉션을 사용 하 여 처리기를 등록 합니다 `ContactsController` 를 통해 [종속성 주입](xref:fundamentals/dependency-injection)합니다. 끝에 다음 코드를 추가 `ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목으로 추가 됩니다. EF를 사용 하지 않는 서 필요한 모든 정보는 단일 항목 들은 합니다 `Context` 의 매개 변수는 `HandleRequirementAsync` 메서드.

## <a name="support-authorization"></a>권한 부여를 지원 합니다.

이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.

### <a name="review-the-contact-operations-requirements-class"></a>연락처 작업 요구 사항 클래스를 검토 합니다.

검토를 `ContactOperations` 클래스입니다. 이 클래스는 포함 요구 사항을 지 원하는:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>연락처 Razor 페이지에 대 한 기본 클래스 만들기

연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다. 초기화 코드를 한 위치에 배치 하는 기본 클래스:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

위의 코드는:

* 추가 된 `IAuthorizationService` 권한 부여 처리기에 액세스 하는 서비스입니다.
* Id 추가 `UserManager` 서비스입니다.
* `ApplicationDbContext`를 추가합니다.

### <a name="update-the-createmodel"></a>업데이트 된 CreateModel

만들기 페이지 모델 생성자를 사용 하 여 업데이트를 `DI_BasePageModel` 기본 클래스:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

업데이트 된 `CreateModel.OnPostAsync` 방법:

* 사용자 ID를 추가 합니다 `Contact` 모델입니다.
* 사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>업데이트 된 IndexModel

업데이트 된 `OnGetAsync` 메서드 승인 된 연락처만 일반 사용자에 게 표시 됩니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>업데이트 된 EditModel

사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다. 리소스 권한 부여가 유효성을 검사 하기 때문에 `[Authorize]` 특성 충분 하지 않습니다. 특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다. 명령적 리소스 기반 권한 부여를 사용 해야 합니다. 페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다. 자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>업데이트 된 DeleteModel

인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>권한 부여 서비스 보기 삽입

현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.

권한 부여 서비스를 주입 합니다 *views/_viewimports.cshtml* 모든 보기에 사용할 수 있도록 파일:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

위의 태그는 몇 개 추가 `using` 문입니다.

업데이트를 **편집** 및 **삭제** 에 연결 *Pages/Contacts/Index.cshtml* 하므로 적절 한 권한 가진 사용자만 렌더링 됩니다.

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다. 링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다. 사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다. Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.

### <a name="update-details"></a>세부 정보 업데이트

관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

업데이트 세부 정보 페이지 모델:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>추가 하거나 역할에 사용자를 제거 합니다.

참조 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 에 대 한 내용은:

* 사용자의 권한을 제거 합니다. 예를 들어 채팅 앱에서 사용자를 음소거 합니다.
* 사용자에 권한을 추가 합니다.

## <a name="test-the-completed-app"></a>완성된 된 앱 테스트

시드 된 사용자 계정의 암호를 이미 설정 하지 않은 경우 사용 합니다 [암호 관리자 도구](xref:security/app-secrets#secret-manager) 암호를 설정 하려면:

* 강력한 암호를 선택 합니다. 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다. 예를 들어 `Passw0rd!` 강력한 암호 요구 사항을 충족 합니다.
* 프로젝트의 폴더에서 다음 명령을 실행 하는 `<PW>` 암호입니다.

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* 데이터베이스 삭제 및 업데이트

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* 데이터베이스를 시드하려면 앱을 다시 시작 합니다.

완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다. 하나의 브라우저에서 새 사용자를 등록 합니다 (예를 들어 `test@example.com`). 다른 사용자와 각 브라우저에 로그인 합니다. 다음 작업을 확인 합니다.

* 등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.
* 관리자 수 승인/거부할 연락처 데이터입니다. 합니다 `Details` 표시를 볼 **승인** 하 고 **거부** 단추입니다.
* 관리자 승인/거부를 편집/삭제할 모든 데이터입니다.

| 사용자                | 앱에서 시드 | 옵션                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 아니요                | 자체 데이터를 편집/삭제 합니다.                |
| manager@contoso.com | 예               | 승인/거부 하 고 편집/삭제할 데이터를 소유 합니다. |
| admin@contoso.com   | 예               | 승인/거부 하 고 모든 데이터를 편집/삭제 합니다. |

관리자의 브라우저에서 연락처를 만듭니다. 삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다. 테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.

## <a name="create-the-starter-app"></a>시작 앱 만들기

* "ContactManager" 라는 Razor 페이지 앱 만들기
  * 응용 프로그램을 만들 **개별 사용자 계정**합니다.
  * 네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.
  * `-uld` SQLite 대신 LocalDB를 지정합니다.

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *모델/연락처를 추가 합니다. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* 스 캐 폴드는 `Contact` 모델입니다.
* 초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* 업데이트를 **ContactManager** 에 고정 합니다 *pages/_layout.cshtml* 파일:

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 만들기, 편집 및 연락처를 삭제 하 여 앱 테스트

### <a name="seed-the-database"></a>데이터베이스 시드

추가 된 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스는 *데이터* 폴더입니다.

호출 `SeedData.Initialize` 에서 `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

응용 프로그램 데이터베이스를 시드를 테스트 합니다. DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>추가 자료

* [Azure App Service에서.NET Core 및 SQL Database 웹 앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core 권한 부여 랩](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다. 이 랩에서이 자습서에 도입 된 보안 기능에 자세한 내용으로 이어집니다.
* <xref:security/authorization/introduction>
* [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies)
