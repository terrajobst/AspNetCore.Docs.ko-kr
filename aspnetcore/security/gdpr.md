---
title: ASP.NET Core에서 지 원하는 일반 데이터 보호 규정 (GDPR)
author: rick-anderson
description: ASP.NET Core 웹 앱에서 GDPR 확장 포인트에 액세스 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 01d2f8943c0995c1400122b89c4ca7c459a85279
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724573"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core에서 EU 데이터 보호 규정 GDPR (일반) 지원

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core의 일부를 충족 하기 위해 Api 및 템플릿을 제공 합니다 [EU 데이터 보호 규정 GDPR (일반)](https://www.eugdpr.org/) 요구 사항:

::: moniker range=">= aspnetcore-3.0"

* 프로젝트 템플릿 확장 지점 및 개인 정보 및 쿠키 정책을 사용 하 여 대체할 수 있는 스텁된 태그를 포함 합니다.
* 합니다 *Pages/Privacy.cshtml* 페이지 또는 *Views/Home/Privacy.cshtml* 보기 사이트의 개인 정보 취급 방침에 자세히 설명 하는 페이지를 제공 합니다.

기능을 사용 하도록 기본 쿠키 동의 ASP.NET Core 3.0 템플릿 생성 된 앱에서 ASP.NET Core 2.2 템플릿을 참조 하십시오.

* 추가 [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 너무 `Startup.ConfigureServices` 하 고 [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 에 `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 쿠키 동의 부분에 추가 합니다 *_Layout.cshtml* 파일:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* 추가 된  *\_CookieConsentPartial.cshtml* 파일을 프로젝트:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 쿠키 동의 기능에 대해 알아보려면이 문서의 ASP.NET Core 2.2 버전을 선택 합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 프로젝트 템플릿 확장 지점 및 개인 정보 및 쿠키 정책을 사용 하 여 대체할 수 있는 스텁된 태그를 포함 합니다.
* 쿠키 동의 기능 수 있습니다 동의 묻는 메시지가 표시 (및 추적) 사용자의 개인 정보를 저장 합니다. 앱에 사용자 데이터 수집에 동의 하지 않은 경우 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) 로 `true`에 필수적이 지 않은 쿠키는 브라우저에 전송 되지 않습니다.
* 기본적인 쿠키를 표시할 수 있습니다. 추적을 사용할 수 없습니다 및 사용자 동의 하지 않은 경우에 필수 쿠키는 브라우저에 전송 됩니다.
* [TempData 및 세션 쿠키](#tempdata) 추적 비활성화 되 면 작동 되지 않습니다.
* 합니다 [Identity 관리](#pd) 페이지를 다운로드 하 여 사용자 데이터를 삭제 한 링크를 제공 합니다.

합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) GDPR 확장 지점 중 대부분을 테스트 하 고 Api ASP.NET Core 2.1 템플릿을 추가할 수 있습니다. 참조를 [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 지침을 테스트 하는 것에 대 한 파일입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>템플릿에서 생성 된 코드에서 ASP.NET Core GDPR 지원

Razor 페이지 및 MVC 프로젝트 템플릿을 사용 하 여 만든 프로젝트에는 다음 GDPR 지원을 포함 합니다.

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 하 고 [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 에서 설정 됩니다는 `Startup` 클래스입니다.
* 합니다  *\_CookieConsentPartial.cshtml* [부분 뷰](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)합니다. **Accept** 단추는이 파일에 포함 됩니다. 클릭할 때 합니다 **Accept** 단추, 쿠키를 저장 하는 동의 제공 됩니다.
* 합니다 *Pages/Privacy.cshtml* 페이지 또는 *Views/Home/Privacy.cshtml* 보기 사이트의 개인 정보 취급 방침에 자세히 설명 하는 페이지를 제공 합니다. 합니다  *\_CookieConsentPartial.cshtml* 파일 개인 정보 페이지에 대 한 링크를 생성 합니다.
* 개별 사용자 계정을 사용 하 여 만든 앱에 대 한 관리 페이지는 다운로드 및 삭제에 대 한 링크를 제공 [개인 사용자 데이터](#pd)입니다.

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions 및 UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 초기화 됩니다 `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 라고 `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial.cshtml 부분 뷰

합니다  *\_CookieConsentPartial.cshtml* 부분 뷰:

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

이 부분:

* 사용자에 대 한 추적의 상태를 가져옵니다. 앱을 동의 요구 하도록 구성 된 경우 사용자는 쿠키를 추적할 수 전에 동의 해야 합니다. 위쪽 탐색 모음에서 만든 쿠키 동의 패널 고정 됩니다. 동의 필요한 경우는  *\_Layout.cshtml* 파일입니다.
* HTML 제공 `<p>` 정책을 사용 하 여 개인 정보 및 쿠키를 요약 하는 요소입니다.
* 개인 정보 페이지 또는 사이트의 개인 정보 취급 방침을 자세히 설명 하 수 있는 보기에 대 한 링크를 제공 합니다.

## <a name="essential-cookies"></a>필수 쿠키

경우 동의 쿠키를 저장할 답변이 제공, 필수로 표시 하는 쿠키가 브라우저에 전달 됩니다. 다음 코드에서는 쿠키 중요 합니다.

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData 공급자 및 세션 상태 쿠키 없는 필수

합니다 [TempData 공급자](xref:fundamentals/app-state#tempdata) 쿠키는 중요 하지 않습니다. 추적을 해제 하면 TempData 공급자 작동 하지 않습니다. 표시의 필수 요소로 TempData 쿠키 TempData 공급자를 추적 비활성화 되 면 사용 하려면 `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[세션 상태](xref:fundamentals/app-state) 쿠키 필수 요소는 아닙니다. 추적을 사용할 수 없습니다. 세션 상태를 작동 하지 않습니다. 다음 코드는 필수 세션 쿠키를 사용 하면:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>개인 데이터

개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 앱 코드를 다운로드 하 여 개인 데이터 삭제를 포함 합니다.

사용자 이름을 선택 하 고 선택한 **개인 데이터**:

![개인 데이터 페이지 관리](gdpr/_static/pd.png)

메모:

* 생성 하는 `Account/Manage` 코드를 참조 하십시오 [스 캐 폴드 Identity](xref:security/authentication/scaffold-identity)합니다.
* 합니다 **삭제** 하 고 **다운로드** 링크는 기본 id 데이터에만 작동 합니다. 앱 사용자 지정 사용자 데이터를 만든 사용자 지정 사용자 데이터를 다운로드/삭제를 확장 해야 합니다. 자세한 내용은 [추가, 다운로드 및 삭제 사용자 지정 사용자 데이터 Id로](xref:security/authentication/add-user-data)합니다.
* Id 데이터베이스 테이블에 저장 된 사용자에 대 한 토큰을 저장 `AspNetUserTokens` 사용자로 인해 연계 delete 동작을 통해 삭제 될 때 삭제 되는 [외래 키](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)합니다.
* [외부 공급자 인증](xref:security/authentication/social/index)Facebook과 Google을 사용할 수 없는 쿠키 정책 수락 되기 전에 같이 합니다.

::: moniker-end

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

일부 데이터베이스 및 저장소 메커니즘에는 휴지 상태의 암호화에 대 한 허용합니다. 미사용 데이터 암호화:

* 저장 된 데이터를 자동으로 암호화합니다.
* 구성, 프로그래밍, 또는 데이터에 액세스 하는 소프트웨어에 대 한 다른 작업 없이 암호화 합니다.
* 쉽고 안전한 옵션이입니다.
* 키 및 암호화를 관리 하기 위해 데이터베이스를 허용 합니다.

예를 들어:

* Microsoft SQL 및 Azure SQL 제공할 [투명 한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [기본적으로 데이터베이스를 암호화 하는 SQL Azure](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure Blob, 파일, Table 및 Queue Storage는 기본적으로 암호화 됩니다](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)합니다.

기본 제공 미사용 데이터 암호화를 제공 하지 않는 데이터베이스에 대 한 디스크 암호화는 동일한 보호를 제공 하는 데 수 있습니다. 예를 들어:

* [Windows Server에 대 한 BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>추가 자료

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
