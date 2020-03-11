---
title: ASP.NET Membership authentication에서 ASP.NET Core 2.0 Id로 마이그레이션
author: isaac2004
description: 멤버 자격 인증을 사용 하 여 기존 ASP.NET apps를 ASP.NET Core 2.0 Id로 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651837"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>ASP.NET Membership authentication에서 ASP.NET Core 2.0 Id로 마이그레이션

작성자: [Isaac Levin](https://isaaclevin.com)

이 문서에서는 ASP.NET Core 2.0 Id에 멤버 자격 인증을 사용 하 여 ASP.NET apps에 대 한 데이터베이스 스키마를 마이그레이션하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 ASP.NET 멤버 기반 앱에 대 한 데이터베이스 스키마를 ASP.NET Core Id에 사용 되는 데이터베이스 스키마로 마이그레이션하는 데 필요한 단계를 제공 합니다. ASP.NET 멤버 기반 인증에서 ASP.NET Identity로 마이그레이션하는 방법에 대 한 자세한 내용은 [SQL 멤버 자격에서 ASP.NET Identity로 기존 앱 마이그레이션](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)을 참조 하세요. ASP.NET Core Id에 대 한 자세한 내용은 [ASP.NET Core의 Id 소개](xref:security/authentication/identity)를 참조 하세요.

## <a name="review-of-membership-schema"></a>멤버 자격 스키마 검토

ASP.NET 2.0 이전에는 개발자가 앱에 대 한 전체 인증 및 권한 부여 프로세스를 만드는 작업을 수행 했습니다. ASP.NET 2.0를 사용 하 여 멤버 자격이 도입 되었으므로 ASP.NET apps 내에서 보안을 처리 하는 상용구 솔루션을 제공 합니다. 개발자는 이제 [aspnet_regsql](https://msdn.microsoft.com/library/ms229862.aspx) 명령을 사용 하 여 스키마를 SQL Server 데이터베이스로 부트스트랩 할 수 있었습니다. 이 명령을 실행 하면 데이터베이스에서 다음 테이블이 생성 됩니다.

  ![멤버 자격 테이블](identity/_static/membership-tables.png)

기존 앱을 ASP.NET Core 2.0 Id로 마이그레이션하려면 새 Id 스키마에서 사용 하는 테이블로 이러한 테이블의 데이터를 마이그레이션해야 합니다.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Id 2.0 스키마

ASP.NET Core 2.0은 ASP.NET 4.5에 도입 된 [id](/aspnet/identity/index) 원칙을 따릅니다. 원칙이 공유 되기는 하지만 프레임 워크 간의 구현은 ASP.NET Core 버전 간에도 다릅니다 ( [인증 및 id를 ASP.NET Core 2.0로 마이그레이션](xref:migration/1x-to-2x/index)참조).

ASP.NET Core 2.0 Id의 스키마를 확인 하는 가장 빠른 방법은 새 ASP.NET Core 2.0 앱을 만드는 것입니다. Visual Studio 2017에서 다음 단계를 수행 합니다.

1. **File** > **New** > **Project**를 선택합니다.
1. *CoreIdentitySample*이라는 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 만듭니다.
1. 드롭다운 목록에서 **ASP.NET Core 2.0** 을 선택 하 고 **웹 응용 프로그램**을 선택 합니다. 이 템플릿은 [Razor Pages](xref:razor-pages/index) 앱을 생성 합니다. **확인**을 클릭 하기 전에 **인증 변경**을 클릭 합니다.
1. Id 템플릿에 대해 **개별 사용자 계정을** 선택 합니다. 마지막으로 **확인**을 클릭 한 다음 **확인**을 클릭 합니다. Visual Studio는 ASP.NET Core Identity 템플릿을 사용 하 여 프로젝트를 만듭니다.
1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택 하 여 **패키지 관리자 콘솔** (PMC) 창을 엽니다.
1. PMC의 프로젝트 루트로 이동 하 고 [EF (Entity Framework) Core](/ef/core) `Update-Database` 명령을 실행 합니다.

    ASP.NET Core 2.0 Id는 EF Core를 사용 하 여 인증 데이터를 저장 하는 데이터베이스와 상호 작용 합니다. 새로 만든 앱이 작동 하려면이 데이터를 저장할 데이터베이스가 있어야 합니다. 새 앱을 만든 후 데이터베이스 환경에서 스키마를 검사 하는 가장 빠른 방법은 [EF Core 마이그레이션을](/ef/core/managing-schemas/migrations/)사용 하 여 데이터베이스를 만드는 것입니다. 이 프로세스는 로컬 또는 다른 위치에 있는 데이터베이스를 만들어 해당 스키마를 모방 합니다. 자세한 내용은 위의 설명서를 참조 하십시오.

    EF Core 명령은 *appsettings*에 지정 된 데이터베이스에 대 한 연결 문자열을 사용 합니다. 다음 연결 문자열은 이름이 *.asp*인 *localhost* 의 데이터베이스를 대상으로 합니다. 이 설정에서는 `DefaultConnection` 연결 문자열을 사용 하도록 EF Core 구성 됩니다.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. **보기** > **SQL Server 개체 탐색기**를 선택 합니다. *Appsettings*의 `ConnectionStrings:DefaultConnection` 속성에 지정 된 데이터베이스 이름에 해당 하는 노드를 확장 합니다.

    `Update-Database` 명령은 스키마를 사용 하 여 지정 된 데이터베이스와 앱을 초기화 하는 데 필요한 모든 데이터를 만들었습니다. 다음 그림에서는 앞의 단계를 사용 하 여 만든 테이블 구조를 보여 줍니다.

    ![Id 테이블](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>스키마 마이그레이션

멤버 자격과 ASP.NET Core Id의 테이블 구조와 필드에는 미묘한 차이가 있습니다. 이 패턴은 ASP.NET 및 ASP.NET Core apps를 사용 하 여 인증/권한 부여에 대해 크게 변경 되었습니다. Id로 계속 사용 되는 키 개체는 *사용자* 및 *역할*입니다. *사용자*, *역할*및 *UserRoles*에 대 한 매핑 테이블은 다음과 같습니다.

### <a name="users"></a>사용자

|*Id<br>(dbo. AspNetUsers*        ||*멤버 자격<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**필드 이름**                 |**형식**|**필드 이름**                                    |**형식**|
|`Id`                           |문자열  |`aspnet_Users.UserId`                             |문자열  |
|`UserName`                     |문자열  |`aspnet_Users.UserName`                           |문자열  |
|`Email`                        |문자열  |`aspnet_Membership.Email`                         |문자열  |
|`NormalizedUserName`           |문자열  |`aspnet_Users.LoweredUserName`                    |문자열  |
|`NormalizedEmail`              |문자열  |`aspnet_Membership.LoweredEmail`                  |문자열  |
|`PhoneNumber`                  |문자열  |`aspnet_Users.MobileAlias`                        |문자열  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> 일부 필드 매핑은 멤버 자격에서 ASP.NET Core Id로 일 대 일 관계와 비슷합니다. 위의 표에서는 기본 멤버 자격 사용자 스키마를 사용 하 여 ASP.NET Core Id 스키마에 매핑합니다. 멤버 자격에 사용 된 다른 모든 사용자 지정 필드는 수동으로 매핑되어야 합니다. 이 매핑에서는 암호 조건과 암호 salts 둘 사이에서 마이그레이션되지 않으므로 암호에 대 한 맵이 없습니다. **암호가 null로 유지 되 고 사용자에 게 암호를 재설정 하도록 요청 하는 것이 좋습니다.** ASP.NET Core Id에서 `LockoutEnd` 사용자가 잠겨 있는 경우 나중에 특정 날짜로 설정 되어야 합니다. 이는 마이그레이션 스크립트에 표시 됩니다.

### <a name="roles"></a>역할

|*Id<br>(dbo. AspNetRoles)*        ||*멤버 자격<br>(dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**필드 이름**                 |**형식**|**필드 이름**   |**형식**         |
|`Id`                           |문자열  |`RoleId`         | 문자열          |
|`Name`                         |문자열  |`RoleName`       | 문자열          |
|`NormalizedName`               |문자열  |`LoweredRoleName`| 문자열          |

### <a name="user-roles"></a>사용자 역할

|*Id<br>(dbo. AspNetUserRoles*||*멤버 자격<br>(dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**필드 이름**           |**형식**  |**필드 이름**|**형식**                   |
|`RoleId`                 |문자열    |`RoleId`      |문자열                     |
|`UserId`                 |문자열    |`UserId`      |문자열                     |

*사용자* 및 *역할*에 대 한 마이그레이션 스크립트를 만들 때 위의 매핑 테이블을 참조 합니다. 다음 예에서는 데이터베이스 서버에 데이터베이스가 두 개 있다고 가정 합니다. 하나의 데이터베이스에는 기존 ASP.NET 멤버 자격 스키마 및 데이터가 포함 됩니다. 다른 *CoreIdentitySample* 데이터베이스는 앞에서 설명한 단계를 사용 하 여 만들어졌습니다. 자세한 내용은 주석이 인라인으로 포함 되어 있습니다.

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

이전 스크립트를 완료 한 후 이전에 만든 ASP.NET Core Identity 앱은 멤버 자격 사용자로 채워집니다. 사용자는 로그인 하기 전에 암호를 변경 해야 합니다.

> [!NOTE]
> 멤버 자격 시스템에 전자 메일 주소와 일치 하지 않는 사용자 이름을 가진 사용자가 있는 경우이를 수용 하기 위해 이전에 만든 앱을 변경 해야 합니다. 기본 템플릿은 `UserName`와 `Email`가 동일 해야 합니다. 서로 다른 경우에는 `Email`대신 `UserName`를 사용 하도록 로그인 프로세스를 수정 해야 합니다.

*Pages\Account\Login.cshtml.cs*에 있는 로그인 페이지의 `PageModel`에서 *전자 메일* 속성의 `[EmailAddress]` 특성을 제거 합니다. 이름을 *UserName*으로 바꿉니다. 이렇게 하려면 *보기* 및 *PageModel*`EmailAddress`에 대 한 변경 내용이 필요 합니다. 결과는 다음과 같이 표시됩니다.

 ![고정 로그인](identity/_static/fixed-login.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SQL 멤버 자격에서 ASP.NET Core 2.0 Id로 사용자를 이식 하는 방법에 대해 알아보았습니다. Id ASP.NET Core에 대 한 자세한 내용은 [Id 소개](xref:security/authentication/identity)를 참조 하세요.
