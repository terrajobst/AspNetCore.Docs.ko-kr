---
title: ASP.NET Core에서 Windows 인증을 구성 합니다.
author: scottaddie
description: ASP.NET Core에서 HTTP.sys 및 IIS에 대 한 Windows 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/29/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 9dfff5dcba409ddca7e05c771b864ab121e0ea85
ms.sourcegitcommit: 06c4f2910dd54ded25e1b8750e09c66578748bc9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66395933"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="50fa6-103">ASP.NET Core에서 Windows 인증을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="50fa6-104">하 여 [Scott Addie](https://twitter.com/Scott_Addie) 고 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50fa6-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50fa6-105">[Windows 인증](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 사용 하 여 호스팅되는 ASP.NET Core 앱에 대해 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index) 하거나 [HTTP.sys](xref:fundamentals/servers/httpsys)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="50fa6-106">Windows 인증은 ASP.NET Core 앱의 사용자를 인증 하는 운영 체제에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="50fa6-107">서버는 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="50fa6-108">Windows 인증은 인트라넷 환경 사용자, 클라이언트 앱 및 웹 서버는 동일한 Windows 도메인에 속해야 하는 위치에 가장 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="launch-settings-debugger"></a><span data-ttu-id="50fa6-109">시작 설정 (디버거)</span><span class="sxs-lookup"><span data-stu-id="50fa6-109">Launch settings (debugger)</span></span>

<span data-ttu-id="50fa6-110">시작 설정에 대 한 구성에만 적용 합니다 *Properties/launchSettings.json* 파일 및 Windows 인증을 위해 IIS 또는 HTTP.sys 서버를 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-110">Configuration for launch settings only affects the *Properties/launchSettings.json* file and doesn't configure the IIS or HTTP.sys server for Windows Authentication.</span></span> <span data-ttu-id="50fa6-111">서버의 구성에 설명 되어는 [IIS 또는 HTTP.sys에 대 한 인증 서비스를 사용 하도록 설정](#authentication-services-for-iis-or-httpsys) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-111">Configuration of the server is explained in the [Enable authentication services for IIS or HTTP.sys](#authentication-services-for-iis-or-httpsys) section.</span></span>

<span data-ttu-id="50fa6-112">합니다 **웹 응용 프로그램** Visual Studio 또는.NET Core CLI를 통해 사용할 수 있는 템플릿을 업데이트 하는 Windows 인증을 지원 하도록 구성할 수 있습니다 합니다 *Properties/launchSettings.json* 파일 자동으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-112">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="50fa6-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50fa6-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="50fa6-114">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="50fa6-114">**New project**</span></span>

1. <span data-ttu-id="50fa6-115">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-115">Create a new project.</span></span>
1. <span data-ttu-id="50fa6-116">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-116">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="50fa6-117">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-117">Select **Next**.</span></span>
1. <span data-ttu-id="50fa6-118">에 이름을 제공 합니다 **프로젝트 이름** 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-118">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="50fa6-119">확인 합니다 **위치** 항목이 올바른 또는 프로젝트의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="50fa6-120">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-120">Select **Create**.</span></span>
1. <span data-ttu-id="50fa6-121">선택 **변경** 아래에서 **인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-121">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="50fa6-122">에 **인증 변경** 창에서 **Windows 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-122">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="50fa6-123">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-123">Select **OK**.</span></span>
1. <span data-ttu-id="50fa6-124">**웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-124">Select **Web Application**.</span></span>
1. <span data-ttu-id="50fa6-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-125">Select **Create**.</span></span>

<span data-ttu-id="50fa6-126">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-126">Run the app.</span></span> <span data-ttu-id="50fa6-127">렌더링 된 앱의 사용자 인터페이스에 사용자 이름이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-127">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="50fa6-128">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="50fa6-128">**Existing project**</span></span>

<span data-ttu-id="50fa6-129">Windows 인증을 사용 하도록 설정 하 고 익명 인증을 사용 하지 않도록 설정 하는 프로젝트의 속성:</span><span class="sxs-lookup"><span data-stu-id="50fa6-129">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="50fa6-130">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-130">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="50fa6-131">**디버그** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-131">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="50fa6-132">에 대 한 확인란의 선택을 취소 **익명 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-132">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="50fa6-133">에 대 한 확인란을 선택 **Windows 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-133">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="50fa6-134">저장 하 고 속성 페이지를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-134">Save and close the property page.</span></span>

<span data-ttu-id="50fa6-135">속성에서 구성할 수 있습니다 또는 합니다 `iisSettings` 의 노드는 *launchSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="50fa6-135">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="50fa6-136">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="50fa6-136">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="50fa6-137">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="50fa6-137">**New project**</span></span>

<span data-ttu-id="50fa6-138">실행 합니다 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령에 `webapp` 인수 (ASP.NET Core 웹 앱) 및 `--auth Windows` 전환:</span><span class="sxs-lookup"><span data-stu-id="50fa6-138">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="50fa6-139">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="50fa6-139">**Existing project**</span></span>

<span data-ttu-id="50fa6-140">업데이트를 `iisSettings` 의 노드는 *launchSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="50fa6-140">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="50fa6-141">기존 프로젝트를 수정 하는 경우 프로젝트 파일에 대 한 패키지 참조를 포함 되어 있는지 확인 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) **하거나** 는 [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="50fa6-141">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="authentication-services-for-iis-or-httpsys"></a><span data-ttu-id="50fa6-142">IIS 또는 HTTP.sys에 대 한 인증 서비스</span><span class="sxs-lookup"><span data-stu-id="50fa6-142">Authentication services for IIS or HTTP.sys</span></span>

<span data-ttu-id="50fa6-143">호스팅 시나리오에 따라 지침을 따릅니다 **중 하나** 는 [IIS](#iis) 섹션 **하거나** [HTTP.sys](#httpsys) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-143">Depending on the hosting scenario, follow the guidance in **either** the [IIS](#iis) section **or** [HTTP.sys](#httpsys) section.</span></span>

### <a name="iis"></a><span data-ttu-id="50fa6-144">IIS</span><span class="sxs-lookup"><span data-stu-id="50fa6-144">IIS</span></span>

<span data-ttu-id="50fa6-145">인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50fa6-145">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="50fa6-146">IIS에서 사용 하 여 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) ASP.NET Core 앱을 호스트 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-146">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="50fa6-147">Windows 인증을 통해 IIS에 대해 구성 된 합니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-147">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="50fa6-148">다음 섹션은 표시 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="50fa6-148">The following sections show how to:</span></span>

* <span data-ttu-id="50fa6-149">로컬 제공할 *web.config* 앱을 배포할 때 서버에서 Windows 인증을 활성화 하는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-149">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="50fa6-150">IIS 관리자를 사용 하 여 구성 하는 *web.config* 서버에 이미 배포 된 ASP.NET Core 앱의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-150">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="50fa6-151">이미 않았다면 IIS을 사용 하 여 ASP.NET Core 앱을 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-151">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="50fa6-152">자세한 내용은 <xref:host-and-deploy/iis/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50fa6-152">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="50fa6-153">Windows 인증을 위해 IIS 역할 서비스를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-153">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="50fa6-154">자세한 내용은 [IIS 역할 서비스 (2 단계 참조)에서 Windows 인증 사용](xref:host-and-deploy/iis/index#iis-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-154">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="50fa6-155">[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 기본적으로 자동으로 요청을 인증 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-155">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="50fa6-156">자세한 내용은 참조 하세요. [IIS 사용 하 여 Windows에서 ASP.NET Core 호스팅. IIS 옵션 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-156">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="50fa6-157">ASP.NET Core 모듈은 기본적으로 앱에 Windows 인증 토큰을 전달 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-157">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="50fa6-158">자세한 내용은 참조 하세요. [ASP.NET Core 모듈 구성 참조: AspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-158">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="50fa6-159">사용 하 여 **하거나** 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="50fa6-159">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="50fa6-160">**게시 하 고 프로젝트를 배포 하기 전에** 다음을 추가 합니다 *web.config* 프로젝트 루트에 파일:</span><span class="sxs-lookup"><span data-stu-id="50fa6-160">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="50fa6-161">.NET Core SDK에 의해 프로젝트를 게시 하는 경우 (없이 `<IsTransformWebConfigDisabled>` 속성이로 설정 `true` 프로젝트 파일에서), 게시 된 *web.config* 파일에는 `<location><system.webServer><security><authentication>` 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-161">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="50fa6-162">에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-162">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="50fa6-163">**게시 및 프로젝트를 배포한 후** IIS 관리자를 사용 하 여 서버 쪽 구성 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-163">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="50fa6-164">IIS 관리자에서 IIS 사이트를 선택 합니다 **사이트** 노드의 합니다 **연결** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-164">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="50fa6-165">두 번 클릭 **Authentication** 에 **IIS** 영역입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-165">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="50fa6-166">선택 **익명 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-166">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="50fa6-167">선택 **사용 안 함** 에 **작업** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-167">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="50fa6-168">**Windows 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-168">Select **Windows Authentication**.</span></span> <span data-ttu-id="50fa6-169">선택 **사용 하도록 설정** 에 **작업** 보충 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-169">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="50fa6-170">이러한 조치로, IIS 관리자 앱의 수정 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-170">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="50fa6-171">A `<system.webServer><security><authentication>` 에 대 한 업데이트 된 설정으로 노드가 추가 됩니다 `anonymousAuthentication` 고 `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="50fa6-171">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="50fa6-172">`<system.webServer>` 섹션에 추가 합니다 *web.config* IIS 관리자에 의해 파일이 앱의 외부 `<location>` 섹션에서는 앱을 게시할 때.NET Core SDK에 의해 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-172">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="50fa6-173">섹션 외부의 추가 되기 때문에 합니다 `<location>` 노드를 설정에서 상속 됩니다 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 현재 앱.</span><span class="sxs-lookup"><span data-stu-id="50fa6-173">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="50fa6-174">상속을 방지 하려면 추가 이동 `<security>` 섹션 내에 `<location><system.webServer>` .NET Core SDK를 제공 하는 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-174">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="50fa6-175">앱에만 적용 IIS 구성을 추가 하려면 IIS 관리자를 사용 하면 *web.config* 서버의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-175">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="50fa6-176">앱의 후속 배포 하는 경우 서버에서 설정을 덮어쓸 수 있습니다 서버의 복사본 *web.config* 프로젝트의 바뀝니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-176">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="50fa6-177">사용 하 여 **하거나** 설정을 관리 하려면 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="50fa6-177">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="50fa6-178">설정을 다시 설정 하려면 IIS 관리자를 사용 합니다 *web.config* 배포에서 파일을 덮어쓸지 후 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-178">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="50fa6-179">추가 된 *web.config 파일* 설정 사용 하 여 로컬 앱.</span><span class="sxs-lookup"><span data-stu-id="50fa6-179">Add a *web.config file* to the app locally with the settings.</span></span>

### <a name="httpsys"></a><span data-ttu-id="50fa6-180">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="50fa6-180">HTTP.sys</span></span>

<span data-ttu-id="50fa6-181">하지만 [Kestrel](xref:fundamentals/servers/kestrel) Windows 인증을 지원 하지 않습니다 사용할 수 있습니다 [HTTP.sys](xref:fundamentals/servers/httpsys) Windows에서 자체 호스팅된 시나리오를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-181">Although [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span>

<span data-ttu-id="50fa6-182">인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50fa6-182">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="50fa6-183">Windows 인증을 사용 하 여 HTTP.sys를 사용 하도록 앱의 웹 호스트 구성 (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="50fa6-183">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="50fa6-184"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 에 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-184"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="50fa6-185">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-185">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="50fa6-186">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-186">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="50fa6-187">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-187">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="50fa6-188">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-188">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="50fa6-189">HTTP.sys는 Nano Server 버전 1709 이상에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-189">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="50fa6-190">Nano Server를 사용 하 여 Windows 인증 및 HTTP.sys를 사용 하려면 사용 된 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-190">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="50fa6-191">Server Core에 대 한 자세한 내용은 참조 하세요. [Windows Server의 Server Core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-191">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="50fa6-192">사용자 권한 부여</span><span class="sxs-lookup"><span data-stu-id="50fa6-192">Authorize users</span></span>

<span data-ttu-id="50fa6-193">익명 액세스 구성 상태는 방식을 결정 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성은 앱에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-193">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="50fa6-194">다음 두 섹션에는 익명 액세스는 허용 되지 않는 및 허용 된 구성 상태를 처리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-194">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="50fa6-195">익명 액세스 허용 안 함</span><span class="sxs-lookup"><span data-stu-id="50fa6-195">Disallow anonymous access</span></span>

<span data-ttu-id="50fa6-196">Windows 인증을 사용 하 고 익명 액세스가 비활성화 되어는 `[Authorize]` 고 `[AllowAnonymous]` 특성은 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-196">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="50fa6-197">IIS 사이트를 익명 액세스를 허용 하지 않도록 구성 된 경우 요청에 하지 앱에 도달 합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-197">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="50fa6-198">이러한 이유로 `[AllowAnonymous]` 특성 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-198">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="50fa6-199">익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="50fa6-199">Allow anonymous access</span></span>

<span data-ttu-id="50fa6-200">Windows 인증 및 익명 액세스를 모두 설정 된 경우 사용 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-200">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="50fa6-201">`[Authorize]` 특성을 사용 하면 앱의 인증을 요구 하는 끝점을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-201">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="50fa6-202">`[AllowAnonymous]` 재정의 특성의 `[Authorize]` 익명 액세스를 허용 하는 앱의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-202">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="50fa6-203">특성 사용 세부 정보를 참조 하세요. <xref:security/authorization/simple>합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-203">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="50fa6-204">기본적으로 권한 부여를 페이지에 액세스할 수 없는 사용자는 빈 HTTP 403 응답으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-204">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="50fa6-205">합니다 [StatusCodePages 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) "액세스 거부" 더 나은 환경을 제공 하기 위해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-205">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="50fa6-206">가장</span><span class="sxs-lookup"><span data-stu-id="50fa6-206">Impersonation</span></span>

<span data-ttu-id="50fa6-207">Asp.net 가장을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-207">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="50fa6-208">앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱의 id를 사용 하 여 앱이 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-208">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="50fa6-209">앱 사용자를 대신 하 여 작업을 수행 해야 하는 경우 사용 하 여 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 에 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 `Startup.Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-209">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="50fa6-210">이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-210">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="50fa6-211">`RunImpersonated` 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-211">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="50fa6-212">예를 들어 전체 요청 또는 미들웨어 체인 래핑 지원 없거나 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-212">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="50fa6-213">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="50fa6-213">Claims transformations</span></span>

<span data-ttu-id="50fa6-214">IIS에서 프로세스 모드를 사용 하 여 호스팅하는 경우 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-214">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="50fa6-215">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-215">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="50fa6-216">In process를 호스트 하는 경우 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은 참조 하세요. <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>합니다.</span><span class="sxs-lookup"><span data-stu-id="50fa6-216">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50fa6-217">추가 자료</span><span class="sxs-lookup"><span data-stu-id="50fa6-217">Additional resources</span></span>

* [<span data-ttu-id="50fa6-218">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="50fa6-218">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
