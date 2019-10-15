---
title: ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지
author: guardrex
description: 외부 공급자에서 추가 클레임 및 토큰을 설정 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: cdf263df8d1aa17ea3820a16ecbd10abce9d683d
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925148"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f87cd-103">ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지</span><span class="sxs-lookup"><span data-stu-id="f87cd-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="f87cd-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="f87cd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f87cd-105">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f87cd-106">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f87cd-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f87cd-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f87cd-108">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f87cd-108">Prerequisites</span></span>

<span data-ttu-id="f87cd-109">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f87cd-110">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f87cd-111">자세한 내용은 <xref:security/authentication/social/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f87cd-112">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f87cd-113">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-113">Set the client ID and client secret</span></span>

<span data-ttu-id="f87cd-114">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f87cd-115">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f87cd-116">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f87cd-117">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f87cd-118">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f87cd-119">Google 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f87cd-120">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f87cd-121">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f87cd-122">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="f87cd-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f87cd-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f87cd-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f87cd-124">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f87cd-125">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="f87cd-125">Establish the authentication scope</span></span>

<span data-ttu-id="f87cd-126">@No__t-0을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f87cd-127">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f87cd-128">공급자</span><span class="sxs-lookup"><span data-stu-id="f87cd-128">Provider</span></span>  | <span data-ttu-id="f87cd-129">Scope</span><span class="sxs-lookup"><span data-stu-id="f87cd-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f87cd-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="f87cd-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f87cd-131">Google</span><span class="sxs-lookup"><span data-stu-id="f87cd-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f87cd-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f87cd-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f87cd-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="f87cd-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f87cd-134">샘플 앱에서 Google의 `userinfo.profile` 범위는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>에서 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>이 호출 될 때 프레임 워크에서 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f87cd-135">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f87cd-136">다음 예제에서는 사용자의 생일을 검색 하기 위해 Google `https://www.googleapis.com/auth/user.birthday.read` 범위가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f87cd-137">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="f87cd-137">Map user data keys and create claims</span></span>

<span data-ttu-id="f87cd-138">공급자의 옵션에서 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대 한 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 또는 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f87cd-139">클레임 유형에 대 한 자세한 내용은 <xref:System.Security.Claims.ClaimTypes>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f87cd-140">샘플 앱은 Google 사용자 데이터의 `locale` 및 `picture` 키에서 로캘 (`urn:google:locale`) 및 그림 (@no__t 1) 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f87cd-141">@No__t-0 <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`)는 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f87cd-142">로그인 프로세스 중에 <xref:Microsoft.AspNetCore.Identity.UserManager%601>은 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>에서 제공 하는 사용자 데이터에 대 한 @no__t 1 클레임을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f87cd-143">샘플 앱에서 `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*)은 <xref:System.Security.Claims.ClaimTypes.GivenName>에 대 한 클레임을 포함 하 여 `ApplicationUser`에 서명 된에 대 한 로캘 (`urn:google:locale`) 및 그림 (`urn:google:picture`) 클레임을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f87cd-144">기본적으로 사용자의 클레임은 인증 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f87cd-145">인증 쿠키가 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f87cd-146">브라우저에서 쿠키 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f87cd-147">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="f87cd-148">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="f87cd-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f87cd-149">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f87cd-150">쿠키 인증 미들웨어의 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>에 대해 사용자 지정 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>을 사용 하 여 요청에 따라 id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f87cd-151">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f87cd-152">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="f87cd-152">Save the access token</span></span>

<span data-ttu-id="f87cd-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>은 권한 부여에 성공 하면 액세스 및 새로 고침 토큰을 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>에 저장 해야 하는지 여부를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f87cd-154">`SaveTokens`은 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로-1 @no__t로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f87cd-155">샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>에서 `SaveTokens`의 값을 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f87cd-156">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f87cd-157">샘플 앱은 *계정/* s s o s o s t o s o s t e r. s e r 1에서 액세스 토큰 `OnPostConfirmationAsync` (새 사용자 등록) 및 `OnGetCallbackAsync` (이전에 등록 한 사용자)을</span><span class="sxs-lookup"><span data-stu-id="f87cd-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f87cd-158">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="f87cd-158">How to add additional custom tokens</span></span>

<span data-ttu-id="f87cd-159">@No__t-0의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 샘플 앱은 현재 <xref:System.DateTime>를 사용 하 여 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>을 `TicketCreated`의 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f87cd-160">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="f87cd-160">Creating and adding claims</span></span>

<span data-ttu-id="f87cd-161">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f87cd-162">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f87cd-163">사용자는 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>에서 파생 하 고 추상 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 메서드를 구현 하 여 사용자 지정 작업을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f87cd-164">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f87cd-165">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="f87cd-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="f87cd-166">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>에 대 한 모든 클레임 작업을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f87cd-167">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>의 클레임을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f87cd-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>은 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 와 함께 사용 되어 프로토콜이 생성 된 클레임을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f87cd-169">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="f87cd-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f87cd-170">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f87cd-171">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f87cd-172">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f87cd-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f87cd-173">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f87cd-173">Prerequisites</span></span>

<span data-ttu-id="f87cd-174">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f87cd-175">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f87cd-176">자세한 내용은 <xref:security/authentication/social/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f87cd-177">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f87cd-178">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-178">Set the client ID and client secret</span></span>

<span data-ttu-id="f87cd-179">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f87cd-180">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f87cd-181">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f87cd-182">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f87cd-183">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f87cd-184">Google 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f87cd-185">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f87cd-186">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="f87cd-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f87cd-187">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="f87cd-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f87cd-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f87cd-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f87cd-189">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f87cd-190">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="f87cd-190">Establish the authentication scope</span></span>

<span data-ttu-id="f87cd-191">@No__t-0을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f87cd-192">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f87cd-193">공급자</span><span class="sxs-lookup"><span data-stu-id="f87cd-193">Provider</span></span>  | <span data-ttu-id="f87cd-194">Scope</span><span class="sxs-lookup"><span data-stu-id="f87cd-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f87cd-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="f87cd-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f87cd-196">Google</span><span class="sxs-lookup"><span data-stu-id="f87cd-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f87cd-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f87cd-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f87cd-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="f87cd-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f87cd-199">샘플 앱에서 Google의 `userinfo.profile` 범위는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>에서 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>이 호출 될 때 프레임 워크에서 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f87cd-200">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f87cd-201">다음 예제에서는 사용자의 생일을 검색 하기 위해 Google `https://www.googleapis.com/auth/user.birthday.read` 범위가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f87cd-202">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="f87cd-202">Map user data keys and create claims</span></span>

<span data-ttu-id="f87cd-203">공급자의 옵션에서 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대 한 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 또는 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f87cd-204">클레임 유형에 대 한 자세한 내용은 <xref:System.Security.Claims.ClaimTypes>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f87cd-205">샘플 앱은 Google 사용자 데이터의 `locale` 및 `picture` 키에서 로캘 (`urn:google:locale`) 및 그림 (@no__t 1) 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f87cd-206">@No__t-0 <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`)는 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-206">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f87cd-207">로그인 프로세스 중에 <xref:Microsoft.AspNetCore.Identity.UserManager%601>은 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>에서 제공 하는 사용자 데이터에 대 한 @no__t 1 클레임을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f87cd-208">샘플 앱에서 `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*)은 <xref:System.Security.Claims.ClaimTypes.GivenName>에 대 한 클레임을 포함 하 여 `ApplicationUser`에 서명 된에 대 한 로캘 (`urn:google:locale`) 및 그림 (`urn:google:picture`) 클레임을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f87cd-209">기본적으로 사용자의 클레임은 인증 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f87cd-210">인증 쿠키가 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f87cd-211">브라우저에서 쿠키 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f87cd-212">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="f87cd-213">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="f87cd-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f87cd-214">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f87cd-215">쿠키 인증 미들웨어의 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>에 대해 사용자 지정 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>을 사용 하 여 요청에 따라 id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f87cd-216">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f87cd-217">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="f87cd-217">Save the access token</span></span>

<span data-ttu-id="f87cd-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>은 권한 부여에 성공 하면 액세스 및 새로 고침 토큰을 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>에 저장 해야 하는지 여부를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f87cd-219">`SaveTokens`은 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로-1 @no__t로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f87cd-220">샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>에서 `SaveTokens`의 값을 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f87cd-221">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f87cd-222">샘플 앱은 *계정/* s s o s o s t o s o s t e r. s e r 1에서 액세스 토큰 `OnPostConfirmationAsync` (새 사용자 등록) 및 `OnGetCallbackAsync` (이전에 등록 한 사용자)을</span><span class="sxs-lookup"><span data-stu-id="f87cd-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f87cd-223">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="f87cd-223">How to add additional custom tokens</span></span>

<span data-ttu-id="f87cd-224">@No__t-0의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 샘플 앱은 현재 <xref:System.DateTime>를 사용 하 여 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>을 `TicketCreated`의 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f87cd-225">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="f87cd-225">Creating and adding claims</span></span>

<span data-ttu-id="f87cd-226">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f87cd-227">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f87cd-228">사용자는 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>에서 파생 하 고 추상 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 메서드를 구현 하 여 사용자 지정 작업을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f87cd-229">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f87cd-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f87cd-230">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="f87cd-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="f87cd-231">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>에 대 한 모든 클레임 작업을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f87cd-232">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>의 클레임을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f87cd-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>은 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 와 함께 사용 되어 프로토콜이 생성 된 클레임을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f87cd-234">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="f87cd-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f87cd-235">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f87cd-235">Additional resources</span></span>

* <span data-ttu-id="f87cd-236">[aspnet/AspNetCore 엔지니어링](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) -1 &ndash; 연결 된 샘플 앱은 [Aspnet/AspNetCore GitHub 리포지토리의](https://github.com/aspnet/AspNetCore) @no__t 3 엔지니어링 분기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-236">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f87cd-237">@No__t-0 분기에는 ASP.NET Core의 다음 릴리스에 대해 활성 개발 중인 코드가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f87cd-238">ASP.NET Core의 릴리스 버전에 대 한 샘플 앱 버전을 보려면 **분기** 드롭다운 목록을 사용 하 여 릴리스 분기 (예: `release/{X.Y}`)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f87cd-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
