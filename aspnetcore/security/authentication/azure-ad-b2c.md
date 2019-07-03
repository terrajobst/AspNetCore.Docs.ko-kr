---
title: ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 클라우드 인증
author: camsoper
description: ASP.NET Core를 사용 하 여 Azure Active Directory B2C 인증을 설정 하는 방법을 알아봅니다.
ms.author: casoper
ms.custom: mvc
ms.date: 02/27/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 3cb878aff7bf0c6c8efe7f3f0c0f06c74acef477
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538739"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="6f65d-103">ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 클라우드 인증</span><span class="sxs-lookup"><span data-stu-id="6f65d-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="6f65d-104">작성자: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="6f65d-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="6f65d-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C)는 웹 및 모바일 앱에 대 한 클라우드 id 관리 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="6f65d-106">서비스는 클라우드 및 온-프레미스에서 호스트 되는 앱에 대 한 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="6f65d-107">인증 유형 포함할 개별 계정, 소셜 네트워크 계정 및 enterprise 계정 페더레이션 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="6f65d-108">또한 Azure AD B2C는 최소 구성으로 multi-factor authentication 인증을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="6f65d-109">Azure Active Directory (Azure AD) 및 Azure AD B2C는 별개 제품입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="6f65d-110">Azure AD 테 넌 트 조직을 나타내고 Azure AD B2C 테 넌 트를 신뢰 당사자 응용 프로그램을 사용 하 여 사용할 id의 컬렉션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="6f65d-111">자세한 내용은를 참조 하세요. [Azure AD B2C: 질문과 대답 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="6f65d-112">이 자습서에 설명 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="6f65d-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f65d-113">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="6f65d-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="6f65d-114">Azure AD B2C에서 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="6f65d-115">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 하는 ASP.NET Core 웹 앱을 만들려면</span><span class="sxs-lookup"><span data-stu-id="6f65d-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="6f65d-116">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="6f65d-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f65d-117">전제 조건</span><span class="sxs-lookup"><span data-stu-id="6f65d-117">Prerequisites</span></span>

<span data-ttu-id="6f65d-118">다음은이 연습에 필요한:</span><span class="sxs-lookup"><span data-stu-id="6f65d-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="6f65d-119">Microsoft Azure 구독</span><span class="sxs-lookup"><span data-stu-id="6f65d-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="6f65d-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="6f65d-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="6f65d-121">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="6f65d-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="6f65d-122">Azure Active Directory B2C 테 넌 트 만들기 [설명서에 설명 된 대로](/azure/active-directory-b2c/active-directory-b2c-get-started)입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="6f65d-123">메시지가 표시 되 면 Azure 구독을 사용 하 여 테 넌 트 연결는 선택 사항이 자습서에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="6f65d-124">Azure AD B2C에서 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="6f65d-125">새로 만든 Azure AD B2C 테 넌 트를 사용 하 여 앱을 등록 [설명서에 나와 있는 단계](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) 아래 합니다 **웹 앱을 등록** 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) under the **Register a web app** section.</span></span> <span data-ttu-id="6f65d-126">중지 된 **웹 앱 클라이언트 비밀 만들기** 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="6f65d-127">클라이언트 암호를이 자습서에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="6f65d-128">다음 값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-128">Use the following values:</span></span>

| <span data-ttu-id="6f65d-129">설정</span><span class="sxs-lookup"><span data-stu-id="6f65d-129">Setting</span></span>                       | <span data-ttu-id="6f65d-130">값</span><span class="sxs-lookup"><span data-stu-id="6f65d-130">Value</span></span>                     | <span data-ttu-id="6f65d-131">노트</span><span class="sxs-lookup"><span data-stu-id="6f65d-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6f65d-132">**이름**</span><span class="sxs-lookup"><span data-stu-id="6f65d-132">**Name**</span></span>                      | <span data-ttu-id="6f65d-133">*&lt;앱 이름&gt;*</span><span class="sxs-lookup"><span data-stu-id="6f65d-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="6f65d-134">입력 한 **이름을** 소비자에 게 앱을 설명 하는 앱에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="6f65d-135">**웹 앱/웹 API 포함**</span><span class="sxs-lookup"><span data-stu-id="6f65d-135">**Include web app / web API**</span></span> | <span data-ttu-id="6f65d-136">예</span><span class="sxs-lookup"><span data-stu-id="6f65d-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="6f65d-137">**암시적 흐름 허용**</span><span class="sxs-lookup"><span data-stu-id="6f65d-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="6f65d-138">예</span><span class="sxs-lookup"><span data-stu-id="6f65d-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="6f65d-139">**회신 URL**</span><span class="sxs-lookup"><span data-stu-id="6f65d-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="6f65d-140">회신 Url은 Azure AD B2C 앱이 요청한 토큰을 반환 하는 위치 끝점입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="6f65d-141">Visual Studio를 사용 하 여 회신 URL을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="6f65d-142">이제 입력 `https://localhost:44300/signin-oidc` 양식을 완성 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="6f65d-143">**앱 ID URI**</span><span class="sxs-lookup"><span data-stu-id="6f65d-143">**App ID URI**</span></span>                | <span data-ttu-id="6f65d-144">비워 둡니다</span><span class="sxs-lookup"><span data-stu-id="6f65d-144">Leave blank</span></span>               | <span data-ttu-id="6f65d-145">이 자습서에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="6f65d-146">**네이티브 클라이언트 포함**</span><span class="sxs-lookup"><span data-stu-id="6f65d-146">**Include native client**</span></span>     | <span data-ttu-id="6f65d-147">아니요</span><span class="sxs-lookup"><span data-stu-id="6f65d-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="6f65d-148">경우 유의 해야 localhost가 아닌 회신 URL을 설정 합니다 [회신 URL 목록에 허용 되는 사항에 대 한 제약 조건을](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span></span> 

<span data-ttu-id="6f65d-149">앱을 등록 한 후에 테 넌 트에서 앱의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="6f65d-150">방금 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-150">Select the app that was just registered.</span></span> <span data-ttu-id="6f65d-151">선택 합니다 **복사** 아이콘의 오른쪽에는 **응용 프로그램 ID** 클립보드에 복사 하는 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="6f65d-152">Nothing 자세히 이때 Azure AD B2C 테 넌 트에서 구성할 수 있지만 브라우저 창을 그대로 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="6f65d-153">ASP.NET Core 앱을 만든 후 더 많은 구성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="6f65d-154">Visual Studio에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="6f65d-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="6f65d-155">Visual Studio 웹 응용 프로그램 템플릿은 인증에 Azure AD B2C 테 넌 트를 사용 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="6f65d-156">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-156">In Visual Studio:</span></span>

1. <span data-ttu-id="6f65d-157">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="6f65d-158">선택 **웹 응용 프로그램** 템플릿 목록에서.</span><span class="sxs-lookup"><span data-stu-id="6f65d-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="6f65d-159">선택 된 **인증 변경** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-159">Select the **Change Authentication** button.</span></span>
    
    ![인증 변경 단추](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="6f65d-161">에 **인증 변경** 대화 상자에서 **개별 사용자 계정**를 선택한 후 **클라우드에서 기존 사용자 저장소에 연결** 드롭다운 목록에서.</span><span class="sxs-lookup"><span data-stu-id="6f65d-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![인증 대화 상자 변경](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="6f65d-163">다음 값으로 양식을 완성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="6f65d-164">설정</span><span class="sxs-lookup"><span data-stu-id="6f65d-164">Setting</span></span>                       | <span data-ttu-id="6f65d-165">값</span><span class="sxs-lookup"><span data-stu-id="6f65d-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="6f65d-166">**도메인 이름**</span><span class="sxs-lookup"><span data-stu-id="6f65d-166">**Domain Name**</span></span>               | <span data-ttu-id="6f65d-167">*&lt;B2C 테 넌 트의 도메인 이름&gt;*</span><span class="sxs-lookup"><span data-stu-id="6f65d-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="6f65d-168">**응용 프로그램 ID**</span><span class="sxs-lookup"><span data-stu-id="6f65d-168">**Application ID**</span></span>            | <span data-ttu-id="6f65d-169">*&lt;클립보드의 응용 프로그램 ID를 붙여 넣습니다.&gt;*</span><span class="sxs-lookup"><span data-stu-id="6f65d-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="6f65d-170">**콜백 경로**</span><span class="sxs-lookup"><span data-stu-id="6f65d-170">**Callback Path**</span></span>             | <span data-ttu-id="6f65d-171">*&lt;기본값 사용&gt;*</span><span class="sxs-lookup"><span data-stu-id="6f65d-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="6f65d-172">**등록 또는 로그인 정책**</span><span class="sxs-lookup"><span data-stu-id="6f65d-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="6f65d-173">**암호 재설정 정책**</span><span class="sxs-lookup"><span data-stu-id="6f65d-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="6f65d-174">**프로필 정책 편집**</span><span class="sxs-lookup"><span data-stu-id="6f65d-174">**Edit profile policy**</span></span>       | <span data-ttu-id="6f65d-175">*&lt;비워 둡니다&gt;*</span><span class="sxs-lookup"><span data-stu-id="6f65d-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="6f65d-176">선택 합니다 **복사본** 링크 옆에 **회신 URI** 회신 URI를 클립보드에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="6f65d-177">선택 **확인** 닫으려면 합니다 **인증 변경** 대화 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="6f65d-178">선택 **확인** 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="6f65d-179">B2C 앱 등록을 완료</span><span class="sxs-lookup"><span data-stu-id="6f65d-179">Finish the B2C app registration</span></span>

<span data-ttu-id="6f65d-180">아직 열려 B2C 앱 속성을 사용 하 여 브라우저 창으로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="6f65d-181">임시 변경 **회신 URL** Visual Studio에서 복사한 이전 값으로 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="6f65d-182">선택 **저장할** 창의 맨 위에 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="6f65d-183">회신 URL을 복사 하지 않은 경우 웹 프로젝트 속성에서 디버그 탭에서 HTTPS 주소를 사용 하 고 추가 합니다 **CallbackPath** 에서 값 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="6f65d-184">정책 구성</span><span class="sxs-lookup"><span data-stu-id="6f65d-184">Configure policies</span></span>

<span data-ttu-id="6f65d-185">Azure AD B2C 설명서의 단계를 사용 [등록 또는 로그인 정책 만들기](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)를 차례로 [암호 재설정 정책 만들기](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span></span> <span data-ttu-id="6f65d-186">에 대 한 설명서에 제공 된 예제 값을 사용할 **Id 공급자**를 **등록 특성**, 및 **응용 프로그램 클레임**합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="6f65d-187">사용 하는 **지금 실행** 설명서에 설명 된 대로 정책을 테스트 하려면 단추는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="6f65d-188">정책 이름이 해당 정책에서 사용 된 설명서에 설명 된 대로 정확 하 게 되어 확인 합니다 **인증 변경** Visual Studio에서 대화 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="6f65d-189">정책 이름을 확인할 수 있습니다 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="6f65d-190">기본 쿠키/JwtBearer/OpenIdConnectOptions 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6f65d-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="6f65d-191">기본 옵션을 직접 구성 하려면에서 적절 한 체계 상수를 사용 하 여 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6f65d-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="6f65d-192">앱 실행</span><span class="sxs-lookup"><span data-stu-id="6f65d-192">Run the app</span></span>

<span data-ttu-id="6f65d-193">Visual Studio에서 눌러 **F5** 를 빌드하고 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="6f65d-194">웹 앱이 시작 되 면 선택 **Accept** (메시지 표시) 하는 경우 쿠키 사용을 받아들이고 선택한 **로그인**합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![앱에 로그인](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="6f65d-196">브라우저에서 Azure AD B2C 테 넌 트 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="6f65d-197">(정책 테스트를 만든) 하는 경우 기존 계정으로 로그인 하거나 선택 **지금 등록** 새 계정을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="6f65d-198">합니다 **암호를 잊으셨나요?** 잊어버린된 암호 재설정 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C 로그인](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="6f65d-200">성공적으로 로그인 한 후 브라우저에서 웹 앱에 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-200">After successfully signing in, the browser redirects to the web app.</span></span>

![성공](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="6f65d-202">다음 단계</span><span class="sxs-lookup"><span data-stu-id="6f65d-202">Next steps</span></span>

<span data-ttu-id="6f65d-203">이 자습서에서는 다음 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f65d-204">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="6f65d-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="6f65d-205">Azure AD B2C에서 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="6f65d-206">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 응용 프로그램을 만들려면</span><span class="sxs-lookup"><span data-stu-id="6f65d-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="6f65d-207">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="6f65d-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="6f65d-208">ASP.NET Core 앱은 인증을 위해 Azure AD B2C를 사용 하도록 구성 했으므로 합니다 [Authorize 특성](xref:security/authorization/simple) 앱 보안을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="6f65d-209">응용 프로그램을 학습 하 여 개발을 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="6f65d-210">[Azure AD B2C 사용자 인터페이스](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="6f65d-211">[암호 복잡성 요구 사항을 구성](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="6f65d-212">[Multi-factor authentication 사용](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="6f65d-213">와 같은 추가 id 공급자를 구성 [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)합니다 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)를 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), 등입니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="6f65d-214">[Azure AD Graph API를 사용 하 여](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 를 Azure AD B2C 테 넌 트에서 그룹 멤버 자격 등의 추가 사용자 정보를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="6f65d-215">[ASP.NET Core web API를 Azure AD B2C를 사용 하 여 보안](xref:security/authentication/azure-ad-b2c-webapi)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-215">[Secure an ASP.NET Core web API using Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span></span>
* <span data-ttu-id="6f65d-216">[Azure AD B2C를 사용 하 여.NET 웹 앱에서.NET web API 호출](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)합니다.</span><span class="sxs-lookup"><span data-stu-id="6f65d-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>
