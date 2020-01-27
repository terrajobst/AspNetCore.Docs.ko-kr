---
title: ASP.NET Core에서 Azure Active Directory B2C를 사용 하는 클라우드 인증
author: camsoper
description: ASP.NET Core를 사용 하 여 Azure Active Directory B2C 인증을 설정 하는 방법을 알아봅니다.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727282"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="e7e0f-103">ASP.NET Core에서 Azure Active Directory B2C를 사용 하는 클라우드 인증</span><span class="sxs-lookup"><span data-stu-id="e7e0f-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="e7e0f-104">작성자: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="e7e0f-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="e7e0f-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C)는 웹 및 모바일 앱에 대 한 클라우드 id 관리 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="e7e0f-106">서비스는 클라우드 및 온-프레미스에서 호스트 되는 앱에 대 한 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="e7e0f-107">인증 유형 포함할 개별 계정, 소셜 네트워크 계정 및 enterprise 계정 페더레이션 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="e7e0f-108">또한 Azure AD B2C는 최소 구성으로 다단계 인증을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="e7e0f-109">Azure Active Directory (Azure AD) 및 Azure AD B2C는 별개 제품입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="e7e0f-110">Azure AD 테 넌 트 조직을 나타내고 Azure AD B2C 테 넌 트를 신뢰 당사자 응용 프로그램을 사용 하 여 사용할 id의 컬렉션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="e7e0f-111">자세한 내용은 참조 하세요 [Azure AD B2C: 질문과 대답 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="e7e0f-112">이 자습서에 설명 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e7e0f-113">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="e7e0f-114">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="e7e0f-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="e7e0f-115">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="e7e0f-116">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="e7e0f-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7e0f-117">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e7e0f-117">Prerequisites</span></span>

<span data-ttu-id="e7e0f-118">다음은이 연습에 필요한:</span><span class="sxs-lookup"><span data-stu-id="e7e0f-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="e7e0f-119">Microsoft Azure 구독</span><span class="sxs-lookup"><span data-stu-id="e7e0f-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="e7e0f-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="e7e0f-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="e7e0f-121">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="e7e0f-122">[설명서에 설명 된 대로](/azure/active-directory-b2c/active-directory-b2c-get-started)Azure Active Directory B2C 테 넌 트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="e7e0f-123">메시지가 표시 되 면 Azure 구독을 사용 하 여 테 넌 트 연결는 선택 사항이 자습서에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="e7e0f-124">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="e7e0f-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="e7e0f-125">새로 만든 Azure AD B2C 테 넌 트에서 설명서의 **웹 앱 등록** 섹션 아래 [에 있는 단계](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) 를 사용 하 여 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="e7e0f-126">중지 된 **웹 앱 클라이언트 비밀 만들기** 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="e7e0f-127">클라이언트 암호를이 자습서에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="e7e0f-128">다음 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-128">Use the following values:</span></span>

| <span data-ttu-id="e7e0f-129">설정</span><span class="sxs-lookup"><span data-stu-id="e7e0f-129">Setting</span></span>                       | <span data-ttu-id="e7e0f-130">값</span><span class="sxs-lookup"><span data-stu-id="e7e0f-130">Value</span></span>                     | <span data-ttu-id="e7e0f-131">참고</span><span class="sxs-lookup"><span data-stu-id="e7e0f-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e7e0f-132">**Name**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-132">**Name**</span></span>                      | <span data-ttu-id="e7e0f-133">*&lt;앱 이름&gt;*</span><span class="sxs-lookup"><span data-stu-id="e7e0f-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="e7e0f-134">입력 한 **이름을** 소비자에 게 앱을 설명 하는 앱에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="e7e0f-135">**웹 앱/웹 API 포함**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-135">**Include web app / web API**</span></span> | <span data-ttu-id="e7e0f-136">예</span><span class="sxs-lookup"><span data-stu-id="e7e0f-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="e7e0f-137">**암시적 흐름 허용**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="e7e0f-138">예</span><span class="sxs-lookup"><span data-stu-id="e7e0f-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="e7e0f-139">**회신 URL**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="e7e0f-140">회신 Url은 Azure AD B2C 앱이 요청한 토큰을 반환 하는 위치 끝점입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="e7e0f-141">Visual Studio는 사용할 회신 URL을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="e7e0f-142">이제 `https://localhost:44300/signin-oidc`를 입력 하 여 양식을 완성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="e7e0f-143">**앱 ID URI**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-143">**App ID URI**</span></span>                | <span data-ttu-id="e7e0f-144">비워 둠</span><span class="sxs-lookup"><span data-stu-id="e7e0f-144">Leave blank</span></span>               | <span data-ttu-id="e7e0f-145">이 자습서에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="e7e0f-146">**네이티브 클라이언트 포함**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-146">**Include native client**</span></span>     | <span data-ttu-id="e7e0f-147">아니요</span><span class="sxs-lookup"><span data-stu-id="e7e0f-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="e7e0f-148">Localhost가 아닌 회신 URL을 설정 하는 경우 [회신 url 목록에서 허용 되는 항목에 대 한 제약 조건을](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="e7e0f-149">앱이 등록 되 면 테 넌 트의 앱 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="e7e0f-150">방금 등록 한 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-150">Select the app that was just registered.</span></span> <span data-ttu-id="e7e0f-151">선택 합니다 **복사** 아이콘의 오른쪽에는 **응용 프로그램 ID** 클립보드에 복사 하는 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="e7e0f-152">지금은 Azure AD B2C 테 넌 트에서 더 이상 구성할 수 없지만 브라우저 창은 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="e7e0f-153">ASP.NET Core 앱을 만든 후에 더 많은 구성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="e7e0f-154">Visual Studio에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="e7e0f-155">Visual Studio 웹 응용 프로그램 템플릿은 인증에 Azure AD B2C 테 넌 트를 사용 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="e7e0f-156">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-156">In Visual Studio:</span></span>

1. <span data-ttu-id="e7e0f-157">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="e7e0f-158">템플릿 목록에서 **웹 응용 프로그램** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="e7e0f-159">선택 된 **인증 변경** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-159">Select the **Change Authentication** button.</span></span>
    
    ![인증 변경 단추](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="e7e0f-161">**인증 변경** 대화 상자에서 **개별 사용자 계정**을 선택한 다음 드롭다운에서 **클라우드의 기존 사용자 저장소에 연결** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![인증 대화 상자 변경](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="e7e0f-163">다음 값으로 양식을 완성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="e7e0f-164">설정</span><span class="sxs-lookup"><span data-stu-id="e7e0f-164">Setting</span></span>                       | <span data-ttu-id="e7e0f-165">값</span><span class="sxs-lookup"><span data-stu-id="e7e0f-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="e7e0f-166">**도메인 이름**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-166">**Domain Name**</span></span>               | <span data-ttu-id="e7e0f-167">*B2C 테 넌 트의 도메인 이름을 &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="e7e0f-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="e7e0f-168">**응용 프로그램 ID**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-168">**Application ID**</span></span>            | <span data-ttu-id="e7e0f-169">*클립보드의 응용 프로그램 ID를 붙여 &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="e7e0f-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="e7e0f-170">**콜백 경로**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-170">**Callback Path**</span></span>             | <span data-ttu-id="e7e0f-171">*기본값을 사용 &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="e7e0f-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="e7e0f-172">**등록 또는 로그인 정책**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="e7e0f-173">**암호 재설정 정책**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="e7e0f-174">**프로필 정책 편집**</span><span class="sxs-lookup"><span data-stu-id="e7e0f-174">**Edit profile policy**</span></span>       | <span data-ttu-id="e7e0f-175">*&lt;비워 둠&gt;*</span><span class="sxs-lookup"><span data-stu-id="e7e0f-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="e7e0f-176">회신 uri 옆의 **복사** 링크를 **선택 하 여** 회신 uri를 클립보드로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="e7e0f-177">선택 **확인** 닫으려면 합니다 **인증 변경** 대화 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="e7e0f-178">선택 **확인** 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="e7e0f-179">B2C 앱 등록을 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-179">Finish the B2C app registration</span></span>

<span data-ttu-id="e7e0f-180">B2C 앱 속성이 계속 열려 있는 브라우저 창으로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="e7e0f-181">이전에 지정한 임시 **회신 URL** 을 Visual Studio에서 복사한 값으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="e7e0f-182">창 맨 위에 있는 **저장** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="e7e0f-183">회신 URL을 복사 하지 않은 경우 웹 프로젝트 속성의 디버그 탭에서 HTTPS 주소를 사용 하 고 *appsettings*의 **callbackpath** 값을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="e7e0f-184">정책 구성</span><span class="sxs-lookup"><span data-stu-id="e7e0f-184">Configure policies</span></span>

<span data-ttu-id="e7e0f-185">Azure AD B2C 설명서의 단계를 사용 하 여 [등록 또는 로그인 정책을 만든](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)다음 [암호 재설정 정책을 만듭니다](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="e7e0f-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="e7e0f-186">에 대 한 설명서에 제공 된 예제 값을 사용할 **Id 공급자**를 **등록 특성**, 및 **응용 프로그램 클레임**합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="e7e0f-187">설명서에 설명 된 대로 **지금 실행** 단추를 사용 하 여 정책을 테스트 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="e7e0f-188">Visual Studio의 **인증 변경** 대화 상자에서 정책 이름이 사용 되었으므로 정책 이름이 설명서에 설명 된 것과 정확 하 게 일치 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="e7e0f-189">정책 이름은 *appsettings*에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="e7e0f-190">기본 OpenIdConnectOptions/JwtBearer/Cookie 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e7e0f-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="e7e0f-191">기본 옵션을 직접 구성 하려면 `Startup.ConfigureServices`에서 적절 한 체계 상수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

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

## <a name="run-the-app"></a><span data-ttu-id="e7e0f-192">앱 실행</span><span class="sxs-lookup"><span data-stu-id="e7e0f-192">Run the app</span></span>

<span data-ttu-id="e7e0f-193">Visual Studio에서 **f5** 키를 눌러 앱을 빌드하고 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="e7e0f-194">웹 앱이 시작 되 면 **동의** 를 선택 하 여 쿠키 사용을 승인한 다음 (메시지가 표시 되는 경우) **로그인**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![앱에 로그인](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="e7e0f-196">브라우저가 Azure AD B2C 테 넌 트로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="e7e0f-197">(정책 테스트를 만든) 하는 경우 기존 계정으로 로그인 하거나 선택 **지금 등록** 새 계정을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="e7e0f-198">합니다 **암호를 잊으셨나요?** 잊어버린된 암호 재설정 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![로그인 Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="e7e0f-200">성공적으로 로그인 하면 브라우저가 웹 앱으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-200">After successfully signing in, the browser redirects to the web app.</span></span>

![성공](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="e7e0f-202">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e7e0f-202">Next steps</span></span>

<span data-ttu-id="e7e0f-203">이 자습서에서는 다음 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e7e0f-204">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="e7e0f-205">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="e7e0f-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="e7e0f-206">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="e7e0f-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="e7e0f-207">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="e7e0f-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="e7e0f-208">ASP.NET Core 앱이 인증을 위해 Azure AD B2C를 사용 하도록 구성 되었으므로 [권한 부여 특성](xref:security/authorization/simple) 을 사용 하 여 앱을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="e7e0f-209">다음을 학습 하 여 앱을 계속 개발 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="e7e0f-210">[Azure AD B2C 사용자 인터페이스](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="e7e0f-211">[암호 복잡성 요구 사항을 구성](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="e7e0f-212">[Multi-factor authentication 사용](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="e7e0f-213">와 같은 추가 id 공급자를 구성 [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)합니다 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)를 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), 등입니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="e7e0f-214">[Azure AD Graph API를 사용 하 여](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 를 Azure AD B2C 테 넌 트에서 그룹 멤버 자격 등의 추가 사용자 정보를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="e7e0f-215">[Azure AD B2C를 사용 하 여 ASP.NET Core WEB API를 보호](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="e7e0f-216">[Azure AD B2C를 사용 하 여.NET 웹 앱에서.NET web API 호출](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7e0f-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>