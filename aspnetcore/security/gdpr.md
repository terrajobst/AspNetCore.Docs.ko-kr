---
title: ASP.NET Core에서 지 원하는 일반 데이터 보호 규정 (GDPR)
author: rick-anderson
description: ASP.NET Core 웹 앱에서 GDPR 확장 포인트에 액세스 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: security/gdpr
ms.openlocfilehash: 967f3246836c93a1af56f7109edb056220606b58
ms.sourcegitcommit: c716ea9155a6b404c1f3d3d34e2388454cd276d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716342"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="af940-103">ASP.NET Core에서 EU 데이터 보호 규정 GDPR (일반) 지원</span><span class="sxs-lookup"><span data-stu-id="af940-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="af940-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af940-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="af940-105">ASP.NET Core의 일부를 충족 하기 위해 Api 및 템플릿을 제공 합니다 [EU 데이터 보호 규정 GDPR (일반)](https://www.eugdpr.org/) 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="af940-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="af940-106">프로젝트 템플릿 확장 지점 및 개인 정보 및 쿠키 정책을 사용 하 여 대체할 수 있는 스텁된 태그를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="af940-107">쿠키 동의 기능 수 있습니다 동의 묻는 메시지가 표시 (및 추적) 사용자의 개인 정보를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="af940-108">앱에 사용자 데이터 수집에 동의 하지 않은 경우 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) 로 `true`에 필수적이 지 않은 쿠키는 브라우저에 전송 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="af940-109">기본적인 쿠키를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="af940-110">추적을 사용할 수 없습니다 및 사용자 동의 하지 않은 경우에 필수 쿠키는 브라우저에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="af940-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="af940-111">[TempData 및 세션 쿠키](#tempdata) 추적 비활성화 되 면 작동 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="af940-112">합니다 [Identity 관리](#pd) 페이지를 다운로드 하 여 사용자 데이터를 삭제 한 링크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="af940-113">합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) GDPR 확장 지점 중 대부분을 테스트 하 고 Api ASP.NET Core 2.1 템플릿을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-113">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="af940-114">참조를 [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 지침을 테스트 하는 것에 대 한 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-114">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="af940-115">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af940-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="af940-116">템플릿에서 생성 된 코드에서 ASP.NET Core GDPR 지원</span><span class="sxs-lookup"><span data-stu-id="af940-116">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="af940-117">Razor 페이지 및 MVC 프로젝트 템플릿을 사용 하 여 만든 프로젝트에는 다음 GDPR 지원을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-117">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="af940-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 하 고 [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 에서 설정 됩니다는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="af940-119">합니다  *\_CookieConsentPartial.cshtml* [부분 뷰](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-119">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="af940-120">**Accept** 단추는이 파일에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="af940-120">An **Accept** button is included in this file.</span></span> <span data-ttu-id="af940-121">클릭할 때 합니다 **Accept** 단추, 쿠키를 저장 하는 동의 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="af940-121">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="af940-122">합니다 *Pages/Privacy.cshtml* 페이지 또는 *Views/Home/Privacy.cshtml* 보기 사이트의 개인 정보 취급 방침에 자세히 설명 하는 페이지를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-122">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="af940-123">합니다  *\_CookieConsentPartial.cshtml* 파일 개인 정보 페이지에 대 한 링크를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-123">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="af940-124">개별 사용자 계정을 사용 하 여 만든 앱에 대 한 관리 페이지는 다운로드 및 삭제에 대 한 링크를 제공 [개인 사용자 데이터](#pd)입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-124">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="af940-125">CookiePolicyOptions 및 UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="af940-125">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="af940-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 초기화 됩니다 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="af940-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="af940-127">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 라고 `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="af940-127">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="af940-128">\_CookieConsentPartial.cshtml 부분 뷰</span><span class="sxs-lookup"><span data-stu-id="af940-128">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="af940-129">합니다  *\_CookieConsentPartial.cshtml* 부분 뷰:</span><span class="sxs-lookup"><span data-stu-id="af940-129">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="af940-130">이 부분:</span><span class="sxs-lookup"><span data-stu-id="af940-130">This partial:</span></span>

* <span data-ttu-id="af940-131">사용자에 대 한 추적의 상태를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="af940-131">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="af940-132">앱을 동의 요구 하도록 구성 된 경우 사용자는 쿠키를 추적할 수 전에 동의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-132">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="af940-133">위쪽 탐색 모음에서 만든 쿠키 동의 패널 고정 됩니다. 동의 필요한 경우는  *\_Layout.cshtml* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-133">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="af940-134">HTML 제공 `<p>` 정책을 사용 하 여 개인 정보 및 쿠키를 요약 하는 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-134">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="af940-135">개인 정보 페이지 또는 사이트의 개인 정보 취급 방침을 자세히 설명 하 수 있는 보기에 대 한 링크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-135">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="af940-136">필수 쿠키</span><span class="sxs-lookup"><span data-stu-id="af940-136">Essential cookies</span></span>

<span data-ttu-id="af940-137">경우 동의 쿠키를 저장할 답변이 제공, 필수로 표시 하는 쿠키가 브라우저에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="af940-137">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="af940-138">다음 코드에서는 쿠키 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-138">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="af940-139">TempData 공급자 및 세션 상태 쿠키 없는 필수</span><span class="sxs-lookup"><span data-stu-id="af940-139">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="af940-140">합니다 [TempData 공급자](xref:fundamentals/app-state#tempdata) 쿠키는 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-140">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="af940-141">추적을 해제 하면 TempData 공급자 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-141">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="af940-142">표시의 필수 요소로 TempData 쿠키 TempData 공급자를 추적 비활성화 되 면 사용 하려면 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="af940-142">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="af940-143">[세션 상태](xref:fundamentals/app-state) 쿠키 필수 요소는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="af940-143">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="af940-144">추적을 사용할 수 없습니다. 세션 상태를 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-144">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="af940-145">다음 코드는 필수 세션 쿠키를 사용 하면:</span><span class="sxs-lookup"><span data-stu-id="af940-145">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="af940-146">개인 데이터</span><span class="sxs-lookup"><span data-stu-id="af940-146">Personal data</span></span>

<span data-ttu-id="af940-147">개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 앱 코드를 다운로드 하 여 개인 데이터 삭제를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-147">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="af940-148">사용자 이름을 선택 하 고 선택한 **개인 데이터**:</span><span class="sxs-lookup"><span data-stu-id="af940-148">Select the user name and then select **Personal data**:</span></span>

![개인 데이터 페이지 관리](gdpr/_static/pd.png)

<span data-ttu-id="af940-150">메모:</span><span class="sxs-lookup"><span data-stu-id="af940-150">Notes:</span></span>

* <span data-ttu-id="af940-151">생성 하는 `Account/Manage` 코드를 참조 하십시오 [스 캐 폴드 Identity](xref:security/authentication/scaffold-identity)합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-151">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="af940-152">합니다 **삭제** 하 고 **다운로드** 링크는 기본 id 데이터에만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-152">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="af940-153">앱 사용자 지정 사용자 데이터를 만든 사용자 지정 사용자 데이터를 다운로드/삭제를 확장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-153">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="af940-154">자세한 내용은 [추가, 다운로드 및 삭제 사용자 지정 사용자 데이터 Id로](xref:security/authentication/add-user-data)합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-154">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="af940-155">Id 데이터베이스 테이블에 저장 된 사용자에 대 한 토큰을 저장 `AspNetUserTokens` 사용자로 인해 연계 delete 동작을 통해 삭제 될 때 삭제 되는 [외래 키](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-155">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="af940-156">[외부 공급자 인증](xref:security/authentication/social/index)Facebook과 Google을 사용할 수 없는 쿠키 정책 수락 되기 전에 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-156">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="af940-157">휴지 상태의 암호화</span><span class="sxs-lookup"><span data-stu-id="af940-157">Encryption at rest</span></span>

<span data-ttu-id="af940-158">일부 데이터베이스 및 저장소 메커니즘에는 휴지 상태의 암호화에 대 한 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-158">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="af940-159">미사용 데이터 암호화:</span><span class="sxs-lookup"><span data-stu-id="af940-159">Encryption at rest:</span></span>

* <span data-ttu-id="af940-160">저장 된 데이터를 자동으로 암호화합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-160">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="af940-161">구성, 프로그래밍, 또는 데이터에 액세스 하는 소프트웨어에 대 한 다른 작업 없이 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-161">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="af940-162">쉽고 안전한 옵션이입니다.</span><span class="sxs-lookup"><span data-stu-id="af940-162">Is the easiest and safest option.</span></span>
* <span data-ttu-id="af940-163">키 및 암호화를 관리 하기 위해 데이터베이스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-163">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="af940-164">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="af940-164">For example:</span></span>

* <span data-ttu-id="af940-165">Microsoft SQL 및 Azure SQL 제공할 [투명 한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="af940-165">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="af940-166">기본적으로 데이터베이스를 암호화 하는 SQL Azure</span><span class="sxs-lookup"><span data-stu-id="af940-166">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="af940-167">[Azure Blob, 파일, Table 및 Queue Storage는 기본적으로 암호화 됩니다](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)합니다.</span><span class="sxs-lookup"><span data-stu-id="af940-167">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="af940-168">기본 제공 미사용 데이터 암호화를 제공 하지 않는 데이터베이스에 대 한 디스크 암호화는 동일한 보호를 제공 하는 데 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="af940-168">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="af940-169">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="af940-169">For example:</span></span>

* [<span data-ttu-id="af940-170">Windows Server에 대 한 BitLocker</span><span class="sxs-lookup"><span data-stu-id="af940-170">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="af940-171">Linux:</span><span class="sxs-lookup"><span data-stu-id="af940-171">Linux:</span></span>
  * [<span data-ttu-id="af940-172">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="af940-172">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="af940-173">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="af940-173">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af940-174">추가 자료</span><span class="sxs-lookup"><span data-stu-id="af940-174">Additional resources</span></span>

* [<span data-ttu-id="af940-175">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="af940-175">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
