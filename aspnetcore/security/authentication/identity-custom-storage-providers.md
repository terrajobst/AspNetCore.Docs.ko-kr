---
title: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자
author: ardalis
description: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자를 구성 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 5a0797fcfe93d49b941b61688ae8f58a1b5d7614
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086343"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core Id는 확장 가능한 시스템 사용자 지정 저장소 공급자를 만들고 앱에 연결할 수 있습니다. 이 항목에서는 ASP.NET Core Id에 대 한 사용자 지정된 저장소 공급자를 만드는 방법을 설명 합니다. 사용자 고유의 저장소 공급자를 만들기 위한 중요 한 개념을 다루지만 단계별 연습은 되지 않습니다.

[GitHub에서 샘플 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)

## <a name="introduction"></a>소개

ASP.NET Core Id 시스템을 기본적으로 Entity Framework Core를 사용 하 여 SQL Server 데이터베이스에 사용자 정보를 저장 합니다. 대부분의 앱에 대 한이 방법은 잘 작동합니다. 그러나 다음 다른 지 속성 메커니즘 또는 데이터 스키마를 사용 하는 것이 좋습니다. 예를 들어:

* 사용할 [Azure Table Storage](/azure/storage/) 또는 다른 데이터 저장소입니다.
* 데이터베이스 테이블 구조가 서로 합니다. 
* 와 같은 다른 데이터 액세스 접근 방식을 사용 하려고 할 수 있습니다 [Dapper](https://github.com/StackExchange/Dapper)합니다. 

이러한 경우 각 저장소 메커니즘에 대 한 사용자 지정된 공급자를 작성할 수 있으며 앱에 해당 공급자를 연결할 수 있습니다.

ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio에서 프로젝트 템플릿에 포함 됩니다.

.NET Core CLI를 사용 하는 경우 추가 `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core Id 아키텍처

ASP.NET Core Id 관리자 및 저장소를 호출 하는 클래스로 구성 됩니다. *관리자* 는 앱 개발자는 Id 사용자를 만드는 등의 작업을 수행 하는 상위 수준 클래스입니다. *저장소* 사용자 역할과 같은 엔터티를 유지 되는 방법을 지정 하는 하위 클래스입니다. 상점과 리포지토리 패턴에 따라 지 속성 메커니즘을 사용 하 여 밀접 하 게 결합 됩니다. 관리자 (구성) 제외 하 고 응용 프로그램 코드 변경 없이 지 속성 메커니즘을 바꿀 수 있습니다 즉 저장소에서 분리 됩니다.

다음 다이어그램에서는 저장소 데이터 액세스 계층 상호 작용 하는 동안 웹 앱 관리자 상호 작용 하는 방법을 보여 줍니다.

![ASP.NET Core 앱 관리자 (예: 'UserManager', 'RoleManager')를 사용 하 여 작동합니다. 관리자는 저장소 (예: ' UserStore') 통신 하는 Entity Framework Core와 같은 라이브러리를 사용 하 여 데이터 원본으로 작동 합니다.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

사용자 지정 저장소 공급자를 만들려면이 데이터 액세스 계층 (위의 다이어그램에서 녹색 및 회색 상자)를 사용 하 여 데이터 원본, 데이터 액세스 계층 및 상호 작용 하는 저장소 클래스를 만듭니다. 관리자나 합니다 (위의 파란색 상자는) 상호 작용 하는 앱 코드를 사용자 지정할 필요가 없습니다.

새 인스턴스를 만들 때 `UserManager` 또는 `RoleManager` 사용자 클래스의 형식을 제공 하 고 저장소 클래스의 인스턴스를 인수로 전달 합니다. 이 방법을 사용 하면 ASP.NET Core에 사용자 지정된 클래스를 연결할 수 있습니다. 

[새 저장소 공급자를 사용 하도록 앱을 다시 구성](#reconfigure-app-to-use-a-new-storage-provider) 인스턴스화하는 방법을 보여 줍니다 `UserManager` 고 `RoleManager` 사용자 지정된 저장소를 사용 하 여 합니다.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Id 데이터 형식 저장

[ASP.NET Core Id](https://github.com/aspnet/identity) 데이터 형식은 다음 섹션에서 자세히 설명 합니다.

### <a name="users"></a>Users

웹 사이트의 사용자를 등록된 합니다. 합니다 [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) 형식은 확장 하거나 사용자 고유의 사용자 지정 형식에 대 한 예제로 사용 될 수 있습니다. 사용자 고유의 사용자 지정 id 저장소 솔루션을 구현 하는 특정 형식에서 상속할 필요가 없습니다.

### <a name="user-claims"></a>사용자 클레임

문 집합 (또는 [클레임](/dotnet/api/system.security.claims.claim)) 사용자의 id를 나타내는 사용자에 대 한 합니다. 사용자의 id 역할을 통해 수행할 수 있습니다 보다 큰 식을 설정할 수 있습니다.

### <a name="user-logins"></a>사용자 로그인

외부 인증 공급자 (예: Facebook 또는 Microsoft 계정)에 대 한 정보는 사용자를 로그인 할 때 사용 하도록 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>역할

사이트에 대 한 권한 부여 그룹입니다. 역할 Id 및 역할 이름 (예: "Admin" 또는 "Employee")를 포함합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>데이터 액세스 계층

이 항목에서는 사용 하고자 하는 지 속성 메커니즘 및 해당 메커니즘에 대 한 엔터티를 만드는 방법에 익숙하다고 가정 합니다. 이 항목에서는 저장소 또는 데이터 액세스 클래스를 만드는 방법에 대 한 정보를 제공 하지 않습니다. ASP.NET Core Id를 사용 하 여 작업할 때 디자인 결정에 대 한 몇 가지 제안을 제공 합니다.

사용자 지정된 저장소 공급자에 대 한 데이터 액세스 계층을 디자인할 때 자유롭게를 해야 합니다. 앱에서 사용 하려는 기능에 대 한 지 속성 메커니즘을 만드는 데만 필요 합니다. 예를 들어 앱에서 역할을 사용 하지 않는 경우에 역할 또는 사용자 역할 연결에 대 한 저장소를 만들 필요가 없습니다. 기술 및 기존 인프라에는 ASP.NET Core Id의 기본 구현에서 매우 다른 구조가 필요할 수 있습니다. 데이터 액세스 계층, 저장소 구현의 구조를 사용 하는 논리를 제공 합니다.

데이터 액세스 계층에는 ASP.NET Core Id에서 데이터 원본에 데이터를 저장 하는 논리를 제공 합니다. 사용자 지정된 저장소 공급자에 대 한 데이터 액세스 계층 사용자 및 역할 정보를 저장 하는 다음 클래스를 포함할 수 있습니다.

### <a name="context-class"></a>Context 클래스

지 속성 메커니즘에 연결 하 여 쿼리를 실행 하는 정보를 캡슐화 합니다. 일반적으로 종속성 주입을 통해 제공 되는이 클래스의 인스턴스를 필요로 하는 여러 데이터 클래스입니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)합니다.

### <a name="user-storage"></a>사용자 저장소

저장 하 고 사용자 정보 (예: 사용자 이름 및 암호 해시)을 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>역할 저장소

저장 하 고 (예: 역할 이름) 역할 정보를 검색 합니다. [예제](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims 저장소

저장 하 고 사용자 클레임 정보 (예: 클레임 유형과 값)을 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins 저장소

저장 하 고 사용자 로그인 정보 (예: 외부 인증 공급자)를 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole 저장소

저장 하 고는 사용자에 게 할당 된 역할을 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**TIP:** 만 앱에서 사용 하려는 클래스를 구현 합니다.

데이터 액세스 클래스에서 지 속성 메커니즘에 대 한 데이터 작업을 수행 하는 코드를 제공 합니다. 예를 들어, 사용자 지정 공급자를 내 해야에서 새 사용자를 만들려면 다음 코드를 *저장할* 클래스:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

사용자를 만들기 위한 구현 논리는 `_usersTable.CreateAsync` 메서드를 아래에 표시 합니다.

## <a name="customize-the-user-class"></a>사용자 클래스를 사용자 지정

저장소 공급자를 구현할 때 해당 하는 사용자 클래스를 만듭니다는 [IdentityUser 클래스](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)합니다.

최소한 user 클래스에 포함 해야 합니다는 `Id` 및 `UserName` 속성입니다.

`IdentityUser` 클래스 속성을 정의 합니다.는 `UserManager` 호출을 수행할 때 작업을 요청 합니다. 기본 형식을 합니다 `Id` 속성은 문자열 이지만에서 상속할 수 있습니다 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 유형을 지정 합니다. 프레임 워크는 데이터 형식 변환을 처리 하기 위해 저장소 구현이 필요 합니다.

## <a name="customize-the-user-store"></a>사용자 저장소를 사용자 지정

만들기는 `UserStore` 사용자에 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다. 이 클래스는 해당 하는 [UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) 클래스입니다. 사용자 `UserStore` 클래스에서 구현 `IUserStore<TUser>` 및 필요한 선택적 인터페이스입니다. 앱에서 제공 하는 기능에 따라 구현 해야 하는 선택적 인터페이스를 선택 합니다.

### <a name="optional-interfaces"></a>선택적 인터페이스

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

선택적 인터페이스에서 상속 `IUserStore<TUser>`합니다. 에 저장 하는 부분적으로 구현 된 샘플 사용자를 볼 수 있습니다 합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)합니다.

내는 `UserStore` 클래스 작업을 수행 하기 위해 만든 데이터 액세스 클래스를 사용 합니다. 이러한 종속성 주입을 사용 하 여 전달 됩니다. Dapper 구현에서는 SQL Server의 예를 들어, 합니다 `UserStore` 클래스에는 `CreateAsync` 의 인스턴스를 사용 하는 메서드 `DapperUsersTable` 새 레코드를 삽입:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>사용자 저장소를 사용자 지정할 때 구현 하는 인터페이스

* **IUserStore**  
 합니다 [IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) 인터페이스는 유일한 인터페이스는 사용자 저장소에 구현 해야 합니다. 만들기, 업데이트, 삭제 및 사용자를 검색 하기 위한 메서드를 정의 합니다.
* **IUserClaimStore**  
 합니다 [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) 인터페이스는 사용자 클레임을 사용 하도록 구현 하는 메서드를 정의 합니다. 추가, 제거 및 사용자 클레임을 검색 하는 메서드를 포함 합니다.
* **IUserLoginStore**  
 합니다 [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) 외부 인증 공급자를 사용 하도록 구현 하는 메서드를 정의 합니다. 추가, 제거 및 사용자 로그인 및 로그인 정보를 기반으로 사용자를 검색 하는 메서드를 검색 하는 메서드를 포함 합니다.
* **IUserRoleStore**  
 합니다 [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) 인터페이스 역할에 사용자를 매핑하기 위해 구현할 메서드를 정의 합니다. 추가, 제거 및 사용자의 역할 및 역할에 사용자가 할당 되었는지 확인 하는 메서드를 검색 하는 메서드를 포함 합니다.
* **IUserPasswordStore**  
 합니다 [IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) 인터페이스 해시 된 암호를 유지 하기 위해 구현할 메서드를 정의 합니다. 가져와 사용자가 암호를 설정 하는지 여부를 나타내는 메서드와 해시 된 암호를 설정 하는 메서드를 포함 합니다.
* **IUserSecurityStampStore**  
 합니다 [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) 인터페이스는 사용자의 계정 정보 변경 되었는지 여부를 나타내는 보안 스탬프를 사용 하기 위해 구현할 메서드를 정의 합니다. 사용자 암호를 변경 또는 추가 하거나 로그인을 제거 하는 경우이 스탬프 업데이트 됩니다. 가져오기 및 설정의 보안 스탬프에 대 한 메서드를 포함 합니다.
* **IUserTwoFactorStore**  
 합니다 [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) 인터페이스 2 단계 인증을 지원 하기 위해 구현할 메서드를 정의 합니다. 가져와 사용자에 대 한 2 단계 인증이 사용 되는지 여부를 설정 하는 메서드를 포함 합니다.
* **IUserPhoneNumberStore**  
 합니다 [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) 인터페이스 사용자 전화 번호를 저장 하는를 구현 하는 메서드를 정의 합니다. 가져오기 및 전화 번호 및 전화 번호가 확인 되었는지 여부를 설정에 대 한 메서드를 포함 합니다.
* **IUserEmailStore**  
 합니다 [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) 인터페이스는 사용자 전자 메일 주소가 저장을 구현 하는 메서드를 정의 합니다. 가져오기 및 전자 메일 주소 및 전자 메일 확인 되었는지 여부를 설정에 대 한 메서드를 포함 합니다.
* **IUserLockoutStore**  
 합니다 [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) 인터페이스 계정 잠금에 대 한 정보를 저장할 구현 하는 메서드를 정의 합니다. 실패 한 액세스 시도 및 잠금을 추적 하기 위한 메서드를 포함 합니다.
* **IQueryableUserStore**  
 합니다 [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) 인터페이스를 쿼리 가능한 사용자 저장소를 제공 하기 위해 구현할 멤버를 정의 합니다.

앱에서 필요한 인터페이스만 구현 합니다. 예를 들어:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin, 및 IdentityUserRole

`Microsoft.AspNet.Identity.EntityFramework` 네임 스페이스의 구현을 포함 합니다 [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), 및 [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) 클래스입니다. 이러한 기능을 사용 하는 경우에 이러한 클래스의 고유한 버전을 만들고 앱에 대 한 속성을 정의 하는 것이 좋습니다. 그러나 경우가 없습니다 (예: 추가 또는 제거 하는 사용자의 클레임) 기본 작업을 수행할 때 이러한 엔터티를 메모리로 로드 하는 것이 효율적입니다. 대신 백 엔드 저장소 클래스는 데이터 원본에서 직접 이러한 작업을 실행할 수 있습니다. 예를 들어 합니다 `UserStore.GetClaimsAsync` 메서드를 호출할 수는 `userClaimTable.FindByUserId(user.Id)` 에서 쿼리를 실행 하는 메서드를 직접 테이블 클레임의 목록을 반환 합니다.

## <a name="customize-the-role-class"></a>역할 클래스를 사용자 지정

역할 저장소 공급자를 구현할 때 사용자 지정 역할 유형을 만들 수 있습니다. 특정 인터페이스를 구현 하지는 필요 하지만 권한이 있어야 합니다는 `Id` 갖습니다 일반적으로 `Name` 속성입니다.

다음은 예제 역할 클래스입니다.

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>역할 저장소를 사용자 지정

만들 수는 `RoleStore` 역할에서 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다. 이 클래스는 해당 하는 [RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) 클래스입니다. 에 `RoleStore` 구현 클래스를 `IRoleStore<TRole>` 및 필요에 따라는 `IQueryableRoleStore<TRole>` 인터페이스입니다.

* **IRoleStore&lt;TRole&gt;**  
 합니다 [IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) 인터페이스 역할 저장소 클래스에서 구현 하는 메서드를 정의 합니다. 만들기, 업데이트, 삭제 및 역할을 검색 하는 메서드를 포함 합니다.
* **RoleStore&lt;TRole&gt;**  
 사용자 지정 하려면 `RoleStore`를 구현 하는 클래스를 만듭니다는 `IRoleStore<TRole>` 인터페이스입니다. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>새 저장소 공급자를 사용 하도록 앱을 다시 구성

저장소 공급자를 구현한 경우에 앱 사용을 구성 합니다. 앱의 기본 공급자를 사용 하는 경우 사용자 지정 공급자를 사용 하 여 대체 합니다.

1. 제거 된 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 패키지.
1. 저장소 공급자는 별도 프로젝트 또는 패키지에 있는 경우 해당 참조를 추가 합니다.
1. 에 대 한 모든 참조를 대체 `Microsoft.AspNetCore.EntityFramework.Identity` 을 사용 하 여 저장소 공급자의 네임 스페이스에 대 한 문입니다.
1. 에 `ConfigureServices` 메서드를 변경 합니다 `AddIdentity` 사용자 지정 형식을 사용 하는 방법입니다. 이 목적을 위해 사용자 고유의 확장 메서드를 만들 수 있습니다. 참조 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 예입니다.
1. 역할을 사용 하는 경우 업데이트를 `RoleManager` 사용 하 여 `RoleStore` 클래스입니다.
1. 앱의 구성에는 연결 문자열 및 자격 증명을 업데이트 합니다.

예제:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>참조

* [ASP.NET 4.x Id에 대 한 사용자 지정 저장소 공급자](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Id](https://github.com/aspnet/identity) &ndash; 이 리포지토리는 커뮤니티에서 저장소 공급자를 유지 관리에 대 한 링크를 포함 합니다.
