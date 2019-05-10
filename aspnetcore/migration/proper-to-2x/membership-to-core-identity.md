---
title: 인증 ASP.NET 멤버 자격에서에서 ASP.NET Core 2.0 Id로 마이그레이션
author: isaac2004
description: ASP.NET Core 2.0 Id 멤버 자격 인증을 사용 하 여 기존 ASP.NET 앱을 마이그레이션하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084872"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>인증 ASP.NET 멤버 자격에서에서 ASP.NET Core 2.0 Id로 마이그레이션

작성자: [Isaac Levin](https://isaaclevin.com)

이 문서에서는 ASP.NET Core 2.0 Id 멤버 자격 인증을 사용 하 여 ASP.NET 앱에 대 한 데이터베이스 스키마를 마이그레이션하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 ASP.NET Core Id에 사용 되는 데이터베이스 스키마를 ASP.NET 멤버 자격 기반 앱에 대 한 데이터베이스 스키마를 마이그레이션하는 데 필요한 단계를 제공 합니다. ASP.NET 멤버 자격 기반 인증에서 ASP.NET Id로 마이그레이션에 대 한 자세한 내용은 참조 하세요. [SQL 멤버 자격에서 ASP.NET Id로 기존 앱을 마이그레이션](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)합니다. ASP.NET Core Id에 대 한 자세한 내용은 참조 하세요. [ASP.NET core Id 소개](xref:security/authentication/identity)합니다.

## <a name="review-of-membership-schema"></a>멤버 자격 스키마 검토

ASP.NET 2.0 이전 개발자를 해당 앱에 대 한 전체 인증 및 권한 부여 프로세스를 작성 해야 했습니다. ASP.NET 2.0을 사용 하 여 ASP.NET 앱 내에서 보안을 처리 하는 상용구 솔루션 제공 멤버 자격 도입 되었습니다. 개발자가 사용 하 여 SQL Server 데이터베이스에 스키마를 부트스트랩 하 이제 있었습니다 합니다 [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) 명령입니다. 이 명령은 실행 한 후 다음 표에 데이터베이스에서 생성 되었습니다.

  ![멤버 자격 테이블](identity/_static/membership-tables.png)

기존 앱을 ASP.NET Core 2.0 Id로 마이그레이션하기 위해 이러한 테이블의 데이터를 새 Id 스키마에서 사용 하는 테이블을 마이그레이션할 해야 합니다.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2.0 스키마

ASP.NET Core 2.0을 따릅니다 합니다 [Identity](/aspnet/identity/index) 원칙 ASP.NET 4.5에서 도입 되었습니다. 프레임 워크 간에 구현은 ASP.NET Core의 버전 간에 다양 한 원칙을 공유 하지만 (참조 [ASP.NET Core 2.0으로 인증 및 Id 마이그레이션](xref:migration/1x-to-2x/index)).

ASP.NET Core 2.0 Id에 대 한 스키마를 볼 수 있는 가장 빠른 방법은 새 ASP.NET Core 2.0 앱을 만드는 것입니다. Visual Studio 2017에서 다음이 단계를 수행 합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
1. 새 **ASP.NET Core 웹 응용 프로그램** 라는 프로젝트가 *CoreIdentitySample*합니다.
1. 선택 **ASP.NET Core 2.0** 한 다음 선택한 드롭다운 **웹 응용 프로그램**합니다. 이 템플릿에서 생성 된 [Razor 페이지](xref:razor-pages/index) 앱. 클릭 하기 전에 **확인**, 클릭 **인증 변경**합니다.
1. 선택할 **개별 사용자 계정** Identity 템플릿에 대 한 합니다. 마지막으로, 클릭 **확인**, 한 다음 **확인**합니다. Visual Studio ASP.NET Core Id 템플릿을 사용 하 여 프로젝트를 만듭니다.
1. 선택 **도구가** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 열려는 **패키지관리자콘솔** (PMC) 창입니다.
1. PMC에서 프로젝트 루트에 이동 하 고 실행 합니다 [EF (Entity Framework) Core](/ef/core) `Update-Database` 명령입니다.

    EF Core를 사용 하 여 인증 데이터를 저장 하는 데이터베이스와 상호 작용 하는 ASP.NET Core 2.0 Id입니다. 새로 만든 앱이 작동 하려면 순서로 필요이 데이터를 저장할 데이터베이스 여야 합니다. 새 앱을 만든 후 데이터베이스 환경에서 스키마를 검사 하는 가장 빠른 방법은 사용 하 여 데이터베이스를 만들 때 [EF Core 마이그레이션](/ef/core/managing-schemas/migrations/)합니다. 이 프로세스에서는 데이터베이스, 로컬로 또는 다른 곳에서 해당 스키마를 모방 하는 합니다. 자세한 내용은 이전 설명서를 검토 합니다.

    지정 된 데이터베이스에 대 한 연결 문자열을 사용 하는 EF Core 명령 *appsettings.json*합니다. 다음 연결 문자열에 데이터베이스를 대상 *localhost* 라는 *asp net-core id*합니다. 이 설정을 사용 하면 EF Core 사용 하도록 구성 되는 `DefaultConnection` 연결 문자열입니다.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. 선택 **뷰** > **SQL Server 개체 탐색기**합니다. 에 지정 된 데이터베이스 이름에 해당 하는 노드를 확장 합니다 `ConnectionStrings:DefaultConnection` 속성을 *appsettings.json*합니다.

    `Update-Database` 앱 초기화에 필요한 모든 데이터 및 스키마를 사용 하 여 지정 된 데이터베이스 명령을 생성 합니다. 다음 이미지에서는 이전 단계를 사용 하 여 만든 테이블 구조를 보여 줍니다.

    ![Identity 테이블](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>스키마 마이그레이션

테이블 구조와 멤버 및 ASP.NET Core Id에 대 한 필드에 미묘한 차이가 있습니다. ASP.NET 및 ASP.NET Core 앱을 사용 하 여 인증/권한 부여에 대 한 패턴은 크게 변경 되었습니다. Id로도 사용 되는 키 개체는 *사용자가* 하 고 *역할*입니다. 매핑 테이블에는 다음과 같습니다 *사용자*하십시오 *역할*, 및 *UserRoles*합니다.

### <a name="users"></a>Users

|*Identity<br>(dbo.AspNetUsers)*        ||*멤버 자격<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**필드 이름**                 |**Type**|**필드 이름**                                    |**Type**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |비트     |`aspnet_Membership.IsLockedOut`                   |비트     |

> [!NOTE]
> 일부 필드 매핑이 ASP.NET Core Id 멤버 자격에서 한 일 관계와 유사합니다. 앞의 표에 기본 멤버 자격 사용자 스키마 및 ASP.NET Core Id 스키마에 매핑됩니다. 멤버 자격에 사용 된 다른 사용자 지정 필드를 수동으로 매핑할 수 해야 합니다. 이 매핑에 암호에 대 한 맵이 없습니다 암호 조건 및 암호 솔트 둘 사이 마이그레이션되지 않습니다. **null로 암호를 그대로 두고 자신의 암호를 재설정 하도록 요청 하려면 해당 하는 것이 좋습니다.** ASP.NET Core Id에 `LockoutEnd` 사용자 잠겨 있으면 일부 미래 날짜를 설정 해야 합니다. 이 작업은 마이그레이션 스크립트에서 표시 됩니다.

### <a name="roles"></a>역할

|*Identity<br>(dbo.AspNetRoles)*        ||*Membership<br>(dbo.aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**필드 이름**                 |**Type**|**필드 이름**   |**Type**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>사용자 역할

|*Identity<br>(dbo.AspNetUserRoles)*||*Membership<br>(dbo.aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**필드 이름**           |**Type**  |**필드 이름**|**Type**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

에 대 한 마이그레이션 스크립트를 만들 때 이전 매핑 테이블을 참조할 *사용자가* 하 고 *역할*입니다. 다음 예제에서는 두 개의 데이터베이스가 데이터베이스 서버에 있는 것을 가정 합니다. 하나의 데이터베이스에는 기존 ASP.NET 멤버 자격 스키마와 데이터를 포함합니다. 다른 *CoreIdentitySample* 앞에서 설명한 단계를 사용 하 여 데이터베이스를 만들었습니다. 주석은 대 한 자세한 내용은 인라인으로 포함된 합니다.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

앞의 스크립트를 완료 하면 앞에서 만든 ASP.NET Core Id 앱은 멤버 자격 사용자를 사용 하 여 채워집니다. 사용자 로그인 시 자신의 암호를 변경 해야 합니다.

> [!NOTE]
> 멤버 자격 시스템에는 사용자가 자신의 전자 메일 주소를 일치 하지 않는 사용자 이름으로,이 수용 하기 위해 앞에서 만든 앱에 필요한 변경 내용이 됩니다. 기본 템플릿을 예상 `UserName` 및 `Email` 동일 해야 합니다. 경우에는 다른 지 알아두면에 대 한 로그인 프로세스를 사용 하 여 수정 해야 `UserName` 대신 `Email`합니다.

에 `PageModel` 위치한 로그인 페이지의 *Pages\Account\Login.cshtml.cs*, 제거를 `[EmailAddress]` 에서 특성을 *전자 메일* 속성. 이 파일 이름을 *UserName*합니다. 이 변경 해야 때마다 `EmailAddress` 에서 언급 되는 *보기* 및 *PageModel*. 결과 다음과 같습니다.

 ![고정된 로그인](identity/_static/fixed-login.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ASP.NET Core 2.0 Id를 SQL 멤버 자격에서 사용자를 이식 하는 방법을 알아보았습니다. ASP.NET Core Id에 대 한 자세한 내용은 [Id 소개](xref:security/authentication/identity)합니다.
