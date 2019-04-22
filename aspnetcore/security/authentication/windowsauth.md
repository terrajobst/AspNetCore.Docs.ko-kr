---
title: ASP.NET Core에서 Windows 인증을 구성 합니다.
author: scottaddie
description: ASP.NET core에서 IIS Express, IIS 및 HTTP.sys를 사용 하 여 Windows 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: bd4ffa79c4d1e0070c820fa9c06b0a84c3aaae74
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468655"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="6cc56-103">ASP.NET Core에서 Windows 인증을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="6cc56-104">하 여 [Scott Addie](https://twitter.com/Scott_Addie) 고 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6cc56-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6cc56-105">[Windows 인증](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 사용 하 여 호스팅되는 ASP.NET Core 앱에 대해 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index) 하거나 [HTTP.sys](xref:fundamentals/servers/httpsys)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="6cc56-106">Windows 인증은 ASP.NET Core 앱의 사용자를 인증 하는 운영 체제에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="6cc56-107">서버는 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="6cc56-108">Windows 인증은 인트라넷 환경 사용자, 클라이언트 앱 및 웹 서버는 동일한 Windows 도메인에 속해야 하는 위치에 가장 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="6cc56-109">ASP.NET Core 앱에서 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="6cc56-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="6cc56-110">합니다 **웹 응용 프로그램** Windows 인증을 지원 하도록 Visual Studio 또는.NET Core CLI를 통해 사용할 수 있는 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6cc56-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6cc56-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="6cc56-112">새 프로젝트를 위해 Windows 인증 앱 템플릿 사용</span><span class="sxs-lookup"><span data-stu-id="6cc56-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="6cc56-113">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-113">In Visual Studio:</span></span>

1. <span data-ttu-id="6cc56-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-114">Create a new project.</span></span>
1. <span data-ttu-id="6cc56-115">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6cc56-116">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-116">Select **Next**.</span></span>
1. <span data-ttu-id="6cc56-117">에 이름을 제공 합니다 **프로젝트 이름** 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-117">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="6cc56-118">확인 합니다 **위치** 항목이 올바른 또는 프로젝트의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-118">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6cc56-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-119">Select **Create**.</span></span>
1. <span data-ttu-id="6cc56-120">선택 **변경** 아래에서 **인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-120">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="6cc56-121">에 **인증 변경** 창에서 **Windows 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-121">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="6cc56-122">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-122">Select **OK**.</span></span>
1. <span data-ttu-id="6cc56-123">**웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-123">Select **Web Application**.</span></span>
1. <span data-ttu-id="6cc56-124">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-124">Select **Create**.</span></span>

<span data-ttu-id="6cc56-125">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-125">Run the app.</span></span> <span data-ttu-id="6cc56-126">렌더링 된 앱의 사용자 인터페이스에 사용자 이름이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-126">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="6cc56-127">기존 프로젝트에 대 한 수동 구성</span><span class="sxs-lookup"><span data-stu-id="6cc56-127">Manual configuration for an existing project</span></span>

<span data-ttu-id="6cc56-128">프로젝트의 속성을 사용 하면 Windows 인증을 사용 하 여 익명 인증을 사용 하지 않도록 설정:</span><span class="sxs-lookup"><span data-stu-id="6cc56-128">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="6cc56-129">Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택한 **속성**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-129">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="6cc56-130">**디버그** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-130">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="6cc56-131">에 대 한 확인란의 선택을 취소 **익명 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-131">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="6cc56-132">에 대 한 확인란을 선택 **Windows 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-132">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="6cc56-133">속성에서 구성할 수 있습니다 또는 합니다 `iisSettings` 의 노드는 *launchSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="6cc56-133">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6cc56-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6cc56-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6cc56-135">사용 된 **Windows 인증** 앱 템플릿.</span><span class="sxs-lookup"><span data-stu-id="6cc56-135">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="6cc56-136">실행 합니다 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령에 `webapp` 인수 (ASP.NET Core 웹 앱) 및 `--auth Windows` 전환:</span><span class="sxs-lookup"><span data-stu-id="6cc56-136">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="6cc56-137">기존 프로젝트를 수정 하는 경우 프로젝트 파일에 대 한 패키지 참조를 포함 되어 있는지 확인 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) **하거나** 는 [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="6cc56-137">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="6cc56-138">IIS 사용 하 여 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="6cc56-138">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="6cc56-139">IIS에서 사용 하 여 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) ASP.NET Core 앱을 호스트 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-139">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="6cc56-140">Windows 인증을 통해 IIS에 대해 구성 된 합니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-140">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="6cc56-141">다음 섹션은 표시 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="6cc56-141">The following sections show how to:</span></span>

* <span data-ttu-id="6cc56-142">로컬 제공할 *web.config* 앱을 배포할 때 서버에서 Windows 인증을 활성화 하는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-142">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="6cc56-143">IIS 관리자를 사용 하 여 구성 하는 *web.config* 서버에 이미 배포 된 ASP.NET Core 앱의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-143">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="6cc56-144">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="6cc56-144">IIS configuration</span></span>

<span data-ttu-id="6cc56-145">이미 않았다면 IIS을 사용 하 여 ASP.NET Core 앱을 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-145">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="6cc56-146">자세한 내용은 <xref:host-and-deploy/iis/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6cc56-146">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="6cc56-147">Windows 인증을 위해 IIS 역할 서비스를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-147">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="6cc56-148">자세한 내용은 [IIS 역할 서비스 (2 단계 참조)에서 Windows 인증 사용](xref:host-and-deploy/iis/index#iis-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-148">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="6cc56-149">IIS 통합 미들웨어는 기본적으로 자동으로 요청을 인증 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-149">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="6cc56-150">자세한 내용은 참조 하세요. [IIS 사용 하 여 Windows에서 ASP.NET Core 호스팅. IIS 옵션 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-150">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="6cc56-151">ASP.NET Core 모듈은 기본적으로 앱에 Windows 인증 토큰을 전달 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-151">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="6cc56-152">자세한 내용은 참조 하세요. [ASP.NET Core 모듈 구성 참조: AspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-152">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="6cc56-153">새 IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="6cc56-153">Create a new IIS site</span></span>

<span data-ttu-id="6cc56-154">이름 및 폴더를 지정 하 고 새 응용 프로그램 풀을 만들 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-154">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="6cc56-155">IIS에서 앱에 대 한 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="6cc56-155">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="6cc56-156">사용 하 여 **하거나** 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="6cc56-156">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="6cc56-157">[앱을 게시 하기 전에 개발 쪽 구성](#development-side-configuration-with-a-local-webconfig-file) (*권장*)</span><span class="sxs-lookup"><span data-stu-id="6cc56-157">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="6cc56-158">앱을 게시 한 후 서버 쪽 구성</span><span class="sxs-lookup"><span data-stu-id="6cc56-158">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="6cc56-159">로컬 web.config 파일을 사용 하 여 개발 쪽 구성</span><span class="sxs-lookup"><span data-stu-id="6cc56-159">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="6cc56-160">다음 단계를 수행 **하기 전에** 있습니다 [게시 하 고 프로젝트를 배포](#publish-and-deploy-your-project-to-the-iis-site-folder)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-160">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="6cc56-161">다음을 추가 합니다 *web.config* 프로젝트 루트에 파일:</span><span class="sxs-lookup"><span data-stu-id="6cc56-161">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="6cc56-162">프로젝트 SDK를 통해 게시 되는 경우 (없이 `<IsTransformWebConfigDisabled>` 속성이로 설정 `true` 프로젝트 파일에서), 게시 된 *web.config* 파일에는 `<location><system.webServer><security><authentication>` 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-162">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="6cc56-163">에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-163">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="6cc56-164">IIS 관리자를 사용 하 여 서버 쪽 구성</span><span class="sxs-lookup"><span data-stu-id="6cc56-164">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="6cc56-165">다음 단계를 수행 **한 후** 있습니다 [게시 하 고 프로젝트를 배포](#publish-and-deploy-your-project-to-the-iis-site-folder)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-165">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="6cc56-166">IIS 관리자에서 IIS 사이트를 선택 합니다 **사이트** 노드의 합니다 **연결** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="6cc56-167">두 번 클릭 **Authentication** 에 **IIS** 영역입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-167">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="6cc56-168">선택 **익명 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="6cc56-169">선택 **사용 안 함** 에 **작업** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-169">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="6cc56-170">**Windows 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="6cc56-171">선택 **사용 하도록 설정** 에 **작업** 보충 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-171">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="6cc56-172">이러한 조치로, IIS 관리자 앱의 수정 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="6cc56-173">A `<system.webServer><security><authentication>` 에 대 한 업데이트 된 설정으로 노드가 추가 됩니다 `anonymousAuthentication` 고 `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="6cc56-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="6cc56-174">`<system.webServer>` 섹션에 추가 합니다 *web.config* IIS 관리자에 의해 파일이 앱의 외부 `<location>` 섹션에서는 앱을 게시할 때.NET Core SDK에 의해 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="6cc56-175">섹션 외부의 추가 되기 때문에 합니다 `<location>` 노드를 설정에서 상속 됩니다 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 현재 앱.</span><span class="sxs-lookup"><span data-stu-id="6cc56-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="6cc56-176">상속을 방지 하려면 추가 이동 `<security>` 섹션 내에 `<location><system.webServer>` SDK에서 제공 하는 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="6cc56-177">앱에만 적용 IIS 구성을 추가 하려면 IIS 관리자를 사용 하면 *web.config* 서버의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="6cc56-178">앱의 후속 배포 하는 경우 서버에서 설정을 덮어쓸 수 있습니다 서버의 복사본 *web.config* 프로젝트의 바뀝니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="6cc56-179">사용 하 여 **하거나** 설정을 관리 하려면 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="6cc56-179">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="6cc56-180">설정을 다시 설정 하려면 IIS 관리자를 사용 합니다 *web.config* 배포에서 파일을 덮어쓸지 후 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="6cc56-181">추가 된 *web.config 파일* 설정 사용 하 여 로컬 앱.</span><span class="sxs-lookup"><span data-stu-id="6cc56-181">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="6cc56-182">자세한 내용은 참조는 [개발 쪽 구성](#development-side-configuration-with-a-local-webconfig-file) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-182">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="6cc56-183">게시 하 고 IIS 사이트 폴더를 프로젝트 배포</span><span class="sxs-lookup"><span data-stu-id="6cc56-183">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="6cc56-184">Visual Studio 또는.NET Core CLI를 사용 하 여, 게시 및 대상 폴더에 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-184">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="6cc56-185">게시 및 배포에는 IIS를 사용 하 여 호스트에 대 한 자세한 내용은 다음 항목을 참조:</span><span class="sxs-lookup"><span data-stu-id="6cc56-185">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="6cc56-186">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="6cc56-186">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="6cc56-187">Windows 인증이 작동을 확인 하려면 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-187">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="6cc56-188">HTTP.sys 사용 하 여 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="6cc56-188">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="6cc56-189">Kestrel은 Windows 인증을 지원 하지 않지만 사용할 수 있습니다 [HTTP.sys](xref:fundamentals/servers/httpsys) Windows에서 자체 호스팅된 시나리오를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-189">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="6cc56-190">다음 예제에서는 Windows 인증을 사용 하 여 HTTP.sys를 사용 하도록 앱의 웹 호스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-190">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="6cc56-191">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-191">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="6cc56-192">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-192">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="6cc56-193">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-193">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="6cc56-194">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-194">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="6cc56-195">HTTP.sys는 Nano Server 버전 1709 이상에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-195">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="6cc56-196">Nano Server를 사용 하 여 Windows 인증 및 HTTP.sys를 사용 하려면 사용 된 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-196">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="6cc56-197">Server Core에 대 한 자세한 내용은 참조 하세요. [Windows Server의 Server Core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-197">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="6cc56-198">Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="6cc56-198">Work with Windows Authentication</span></span>

<span data-ttu-id="6cc56-199">익명 액세스 구성 상태는 방식을 결정 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성은 앱에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-199">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="6cc56-200">다음 두 섹션에는 익명 액세스는 허용 되지 않는 및 허용 된 구성 상태를 처리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-200">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="6cc56-201">익명 액세스 허용 안 함</span><span class="sxs-lookup"><span data-stu-id="6cc56-201">Disallow anonymous access</span></span>

<span data-ttu-id="6cc56-202">Windows 인증을 사용 하 고 익명 액세스가 비활성화 되어는 `[Authorize]` 고 `[AllowAnonymous]` 특성은 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-202">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="6cc56-203">IIS 사이트 (또는 HTTP.sys)를 익명 액세스를 허용 하도록 구성 하는 경우 요청에 하지 앱에 도달 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-203">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="6cc56-204">이러한 이유로 `[AllowAnonymous]` 특성 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-204">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="6cc56-205">익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="6cc56-205">Allow anonymous access</span></span>

<span data-ttu-id="6cc56-206">Windows 인증 및 익명 액세스를 모두 설정 된 경우 사용 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-206">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="6cc56-207">`[Authorize]` 특성을 사용 하면 Windows 인증 필요 실제로 앱의 부분을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-207">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="6cc56-208">합니다 `[AllowAnonymous]` 재정의 특성 `[Authorize]` 특성 익명 액세스를 허용 하는 앱 내에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-208">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="6cc56-209">참조 [단순 권한 부여](xref:security/authorization/simple) 자세한 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="6cc56-209">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="6cc56-210">ASP.NET Core에서 2.x의 경우는 `[Authorize]` 특성 추가 구성이 필요 *Startup.cs* Windows 인증에 대 한 익명 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-210">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="6cc56-211">권장된 구성에 사용 중인 웹 서버에 따라 약간씩 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-211">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="6cc56-212">기본적으로 권한 부여를 페이지에 액세스할 수 없는 사용자는 빈 HTTP 403 응답으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-212">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="6cc56-213">합니다 [StatusCodePages 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) "액세스 거부" 더 나은 환경을 제공 하기 위해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-213">The [StatusCodePages middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="6cc56-214">IIS</span><span class="sxs-lookup"><span data-stu-id="6cc56-214">IIS</span></span>

<span data-ttu-id="6cc56-215">IIS를 사용 하는 경우 다음을 추가 하 여 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="6cc56-215">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="6cc56-216">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="6cc56-216">HTTP.sys</span></span>

<span data-ttu-id="6cc56-217">HTTP.sys를 사용 하는 경우 다음을 추가 하 여 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="6cc56-217">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="6cc56-218">가장</span><span class="sxs-lookup"><span data-stu-id="6cc56-218">Impersonation</span></span>

<span data-ttu-id="6cc56-219">Asp.net 가장을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-219">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="6cc56-220">앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱의 id를 사용 하 여 앱이 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-220">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="6cc56-221">사용 하 여 명시적으로 사용자를 대신 하 여 작업을 수행 해야 할 경우 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 에 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 `Startup.Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-221">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="6cc56-222">이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-222">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="6cc56-223">`RunImpersonated` 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-223">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="6cc56-224">예를 들어 전체 요청 또는 미들웨어 체인 래핑 지원 없거나 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-224">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

### <a name="claims-transformations"></a><span data-ttu-id="6cc56-225">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="6cc56-225">Claims transformations</span></span>

<span data-ttu-id="6cc56-226">IIS에서 프로세스 모드를 사용 하 여 호스팅하는 경우 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-226">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="6cc56-227">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-227">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="6cc56-228">In process를 호스트 하는 경우 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은 참조 하세요. <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>합니다.</span><span class="sxs-lookup"><span data-stu-id="6cc56-228">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>
