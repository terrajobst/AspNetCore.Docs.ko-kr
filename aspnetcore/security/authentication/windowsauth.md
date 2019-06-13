---
title: ASP.NET Core에서 Windows 인증을 구성 합니다.
author: scottaddie
description: ASP.NET Core에서 HTTP.sys 및 IIS에 대 한 Windows 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/12/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 93f833adff95f25d570947cd1a9035d652f522c2
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034956"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="5797d-103">ASP.NET Core에서 Windows 인증을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="5797d-104">하 여 [Scott Addie](https://twitter.com/Scott_Addie) 고 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5797d-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5797d-105">사용 하 여 호스팅되는 ASP.NET Core 앱에 대 한 Windows 인증 (라고도: Negotiate, Kerberos 또는 NTLM 인증)를 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index)하십시오 [Kestrel](xref:fundamentals/servers/kestrel), 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="5797d-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5797d-106">사용 하 여 호스팅되는 ASP.NET Core 앱에 대 한 Windows 인증 (라고도: Negotiate, Kerberos 또는 NTLM 인증)를 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index) 하거나 [HTTP.sys](xref:fundamentals/servers/httpsys)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="5797d-107">Windows 인증은 ASP.NET Core 앱의 사용자를 인증 하는 운영 체제에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="5797d-108">서버는 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="5797d-109">Windows 인증은 인트라넷 환경 사용자, 클라이언트 앱 및 웹 서버는 동일한 Windows 도메인에 속해야 하는 위치에 가장 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="5797d-110">HTTP/2를 사용 하 여 Windows 인증이 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="5797d-111">HTTP/2 응답에서 인증 질문을 보낼 수 있습니다 하지만 인증 하기 전에 클라이언트 HTTP/1.1로 다운 그레이드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="5797d-112">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="5797d-112">IIS/IIS Express</span></span>

<span data-ttu-id="5797d-113">인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5797d-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="5797d-114">시작 설정 (디버거)</span><span class="sxs-lookup"><span data-stu-id="5797d-114">Launch settings (debugger)</span></span>

<span data-ttu-id="5797d-115">시작 설정에 대 한 구성에만 적용 합니다 *Properties/launchSettings.json* IIS Express에 대 한 파일을 IIS에 대 한 Windows 인증을 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="5797d-116">서버 구성에 설명 되어는 [IIS](#iis) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="5797d-117">합니다 **웹 응용 프로그램** Visual Studio 또는.NET Core CLI를 통해 사용할 수 있는 템플릿을 업데이트 하는 Windows 인증을 지원 하도록 구성할 수 있습니다 합니다 *Properties/launchSettings.json* 파일 자동으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5797d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5797d-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5797d-119">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="5797d-119">**New project**</span></span>

1. <span data-ttu-id="5797d-120">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-120">Create a new project.</span></span>
1. <span data-ttu-id="5797d-121">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5797d-122">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-122">Select **Next**.</span></span>
1. <span data-ttu-id="5797d-123">에 이름을 제공 합니다 **프로젝트 이름** 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="5797d-124">확인 합니다 **위치** 항목이 올바른 또는 프로젝트의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5797d-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-125">Select **Create**.</span></span>
1. <span data-ttu-id="5797d-126">선택 **변경** 아래에서 **인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="5797d-127">에 **인증 변경** 창에서 **Windows 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="5797d-128">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-128">Select **OK**.</span></span>
1. <span data-ttu-id="5797d-129">**웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="5797d-130">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-130">Select **Create**.</span></span>

<span data-ttu-id="5797d-131">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-131">Run the app.</span></span> <span data-ttu-id="5797d-132">렌더링 된 앱의 사용자 인터페이스에 사용자 이름이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="5797d-133">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="5797d-133">**Existing project**</span></span>

<span data-ttu-id="5797d-134">Windows 인증을 사용 하도록 설정 하 고 익명 인증을 사용 하지 않도록 설정 하는 프로젝트의 속성:</span><span class="sxs-lookup"><span data-stu-id="5797d-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="5797d-135">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="5797d-136">**디버그** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="5797d-137">에 대 한 확인란의 선택을 취소 **익명 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="5797d-138">에 대 한 확인란을 선택 **Windows 인증 사용**합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="5797d-139">저장 하 고 속성 페이지를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-139">Save and close the property page.</span></span>

<span data-ttu-id="5797d-140">속성에서 구성할 수 있습니다 또는 합니다 `iisSettings` 의 노드는 *launchSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="5797d-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="5797d-141">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5797d-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="5797d-142">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="5797d-142">**New project**</span></span>

<span data-ttu-id="5797d-143">실행 합니다 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령에 `webapp` 인수 (ASP.NET Core 웹 앱) 및 `--auth Windows` 전환:</span><span class="sxs-lookup"><span data-stu-id="5797d-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="5797d-144">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="5797d-144">**Existing project**</span></span>

<span data-ttu-id="5797d-145">업데이트를 `iisSettings` 의 노드는 *launchSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="5797d-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="5797d-146">기존 프로젝트를 수정 하는 경우 프로젝트 파일에 대 한 패키지 참조를 포함 되어 있는지 확인 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) **하거나** 는 [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="5797d-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="5797d-147">IIS</span><span class="sxs-lookup"><span data-stu-id="5797d-147">IIS</span></span>

<span data-ttu-id="5797d-148">IIS에서 사용 하 여 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) ASP.NET Core 앱을 호스트 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="5797d-149">Windows 인증을 통해 IIS에 대해 구성 된 합니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="5797d-150">다음 섹션은 표시 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="5797d-150">The following sections show how to:</span></span>

* <span data-ttu-id="5797d-151">로컬 제공할 *web.config* 앱을 배포할 때 서버에서 Windows 인증을 활성화 하는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="5797d-152">IIS 관리자를 사용 하 여 구성 하는 *web.config* 서버에 이미 배포 된 ASP.NET Core 앱의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="5797d-153">이미 않았다면 IIS을 사용 하 여 ASP.NET Core 앱을 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="5797d-154">자세한 내용은 <xref:host-and-deploy/iis/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5797d-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="5797d-155">Windows 인증을 위해 IIS 역할 서비스를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="5797d-156">자세한 내용은 [IIS 역할 서비스 (2 단계 참조)에서 Windows 인증 사용](xref:host-and-deploy/iis/index#iis-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="5797d-157">[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 기본적으로 자동으로 요청을 인증 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="5797d-158">자세한 내용은 참조 하세요. [IIS 사용 하 여 Windows에서 ASP.NET Core 호스팅. IIS 옵션 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="5797d-159">ASP.NET Core 모듈은 기본적으로 앱에 Windows 인증 토큰을 전달 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="5797d-160">자세한 내용은 참조 하세요. [ASP.NET Core 모듈 구성 참조: AspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="5797d-161">사용 하 여 **하거나** 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="5797d-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="5797d-162">**게시 하 고 프로젝트를 배포 하기 전에** 다음을 추가 합니다 *web.config* 프로젝트 루트에 파일:</span><span class="sxs-lookup"><span data-stu-id="5797d-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="5797d-163">.NET Core SDK에 의해 프로젝트를 게시 하는 경우 (없이 `<IsTransformWebConfigDisabled>` 속성이로 설정 `true` 프로젝트 파일에서), 게시 된 *web.config* 파일에는 `<location><system.webServer><security><authentication>` 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="5797d-164">에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="5797d-165">**게시 및 프로젝트를 배포한 후** IIS 관리자를 사용 하 여 서버 쪽 구성 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="5797d-166">IIS 관리자에서 IIS 사이트를 선택 합니다 **사이트** 노드의 합니다 **연결** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="5797d-167">두 번 클릭 **Authentication** 에 **IIS** 영역입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="5797d-168">선택 **익명 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="5797d-169">선택 **사용 안 함** 에 **작업** 사이드바 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="5797d-170">**Windows 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="5797d-171">선택 **사용 하도록 설정** 에 **작업** 보충 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="5797d-172">이러한 조치로, IIS 관리자 앱의 수정 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="5797d-173">A `<system.webServer><security><authentication>` 에 대 한 업데이트 된 설정으로 노드가 추가 됩니다 `anonymousAuthentication` 고 `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="5797d-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="5797d-174">`<system.webServer>` 섹션에 추가 합니다 *web.config* IIS 관리자에 의해 파일이 앱의 외부 `<location>` 섹션에서는 앱을 게시할 때.NET Core SDK에 의해 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="5797d-175">섹션 외부의 추가 되기 때문에 합니다 `<location>` 노드를 설정에서 상속 됩니다 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 현재 앱.</span><span class="sxs-lookup"><span data-stu-id="5797d-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="5797d-176">상속을 방지 하려면 추가 이동 `<security>` 섹션 내에 `<location><system.webServer>` .NET Core SDK를 제공 하는 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="5797d-177">앱에만 적용 IIS 구성을 추가 하려면 IIS 관리자를 사용 하면 *web.config* 서버의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="5797d-178">앱의 후속 배포 하는 경우 서버에서 설정을 덮어쓸 수 있습니다 서버의 복사본 *web.config* 프로젝트의 바뀝니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="5797d-179">사용 하 여 **하거나** 설정을 관리 하려면 다음 방법 중:</span><span class="sxs-lookup"><span data-stu-id="5797d-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="5797d-180">설정을 다시 설정 하려면 IIS 관리자를 사용 합니다 *web.config* 배포에서 파일을 덮어쓸지 후 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="5797d-181">추가 된 *web.config 파일* 설정 사용 하 여 로컬 앱.</span><span class="sxs-lookup"><span data-stu-id="5797d-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="5797d-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5797d-182">Kestrel</span></span>

 <span data-ttu-id="5797d-183">합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 패키지 사용 될 수 있습니다 [Kestrel](xref:fundamentals/servers/kestrel) Negotiate, Kerberos 및 NTLM을 사용 하 여 Windows, Linux 및 macOS에서 Windows 인증을 지원 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="5797d-184">연결에 대 한 요청에서 자격 증명을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="5797d-185">*협상 프록시 Kestrel 사용 하 여 1:1 연결 선호도 (영구 연결)를 유지 하지 않는 인증 프록시를 사용 하 여 사용 하면 안 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="5797d-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span> <span data-ttu-id="5797d-186">즉, Negotiate 인증을 IIS 뒤에 있는 Kestrel을 사용 하 여 사용할 해야 [ancm (ASP.NET Core 모듈은) out of process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-186">This means that Negotiate authentication must not be used with Kestrel behind the IIS [ASP.NET Core Module (ANCM) out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model).</span></span>

 <span data-ttu-id="5797d-187">인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` 네임 스페이스) 및 `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` 네임 스페이스)에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5797d-187">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="5797d-188">호출 하 여 인증 미들웨어를 추가할 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 에서 `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5797d-188">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="5797d-189">미들웨어에 대 한 자세한 내용은 참조 하세요. <xref:fundamentals/middleware/index>합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-189">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5797d-190">익명 요청 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-190">Anonymous requests are allowed.</span></span> <span data-ttu-id="5797d-191">사용 하 여 [ASP.NET Core 권한 부여](xref:security/authorization/introduction) 인증에 대 한 익명 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-191">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="5797d-192">Windows 환경 구성</span><span class="sxs-lookup"><span data-stu-id="5797d-192">Windows environment configuration</span></span>

<span data-ttu-id="5797d-193">합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 사용자 모드 인증을 수행 하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-193">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="5797d-194">서비스 사용자 이름 (Spn) 컴퓨터 계정이 아닌 서비스를 실행 하는 사용자 계정에 추가 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-194">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="5797d-195">실행 `setspn -S HTTP/mysrevername.mydomain.com myuser` 관리 명령 셸에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-195">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="5797d-196">Linux 및 macOS 환경 구성</span><span class="sxs-lookup"><span data-stu-id="5797d-196">Linux and macOS environment configuration</span></span>

<span data-ttu-id="5797d-197">Linux 또는 macOS 컴퓨터를 Windows 도메인 가입에 대 한 지침은 합니다 [Kerberos Windows 인증을 사용 하 여 SQL Server를 Azure Data Studio 연결](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서.</span><span class="sxs-lookup"><span data-stu-id="5797d-197">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="5797d-198">지침을 도메인에 Linux 컴퓨터에 대 한 컴퓨터 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-198">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="5797d-199">해당 컴퓨터 계정에 Spn은 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-199">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="5797d-200">지침에 따라 하는 경우는 [Azure Data Studio Kerberos Windows 인증을 사용 하 여 SQL Server에 연결](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서를 교체 `python-software-properties` 사용 하 여 `python3-software-properties` 필요한 경우.</span><span class="sxs-lookup"><span data-stu-id="5797d-200">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="5797d-201">제공 하는 데 필요한 추가 단계는 Linux 또는 macOS 컴퓨터에 도메인에 가입 되 면을 [keytab 파일](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) Spn을 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="5797d-201">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="5797d-202">도메인 컨트롤러에서 컴퓨터 계정에 새 웹 서비스 Spn을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-202">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="5797d-203">사용 하 여 [ktpass](/windows-server/administration/windows-commands/ktpass) keytab 파일을 생성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5797d-203">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="5797d-204">표시 된 대로 대문자로에서 일부 필드를 지정 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-204">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="5797d-205">Linux 또는 macOS 컴퓨터에 키 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-205">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="5797d-206">환경 변수를 통해 keytab 파일을 선택 합니다. `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="5797d-206">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="5797d-207">호출 `klist` 현재 사용 가능한 Spn을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-207">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="5797d-208">Keytab 파일을 도메인 액세스 자격 증명을 포함 하 고 적절 하 게 보호 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-208">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="5797d-209">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="5797d-209">HTTP.sys</span></span>

<span data-ttu-id="5797d-210">[HTTP.sys](xref:fundamentals/servers/httpsys) 협상, NTLM 또는 기본 인증을 사용 하 여 커널 모드 Windows 인증을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-210">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="5797d-211">인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5797d-211">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="5797d-212">Windows 인증을 사용 하 여 HTTP.sys를 사용 하도록 앱의 웹 호스트 구성 (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="5797d-212">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="5797d-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 에 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5797d-214">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-214">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="5797d-215">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-215">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="5797d-216">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-216">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="5797d-217">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-217">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5797d-218">HTTP.sys는 Nano Server 버전 1709 이상에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-218">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="5797d-219">Nano Server를 사용 하 여 Windows 인증 및 HTTP.sys를 사용 하려면 사용 된 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-219">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="5797d-220">Server Core에 대 한 자세한 내용은 참조 하세요. [Windows Server의 Server Core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-220">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="5797d-221">사용자 권한 부여</span><span class="sxs-lookup"><span data-stu-id="5797d-221">Authorize users</span></span>

<span data-ttu-id="5797d-222">익명 액세스 구성 상태는 방식을 결정 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성은 앱에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-222">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="5797d-223">다음 두 섹션에는 익명 액세스는 허용 되지 않는 및 허용 된 구성 상태를 처리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-223">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="5797d-224">익명 액세스 허용 안 함</span><span class="sxs-lookup"><span data-stu-id="5797d-224">Disallow anonymous access</span></span>

<span data-ttu-id="5797d-225">Windows 인증을 사용 하 고 익명 액세스가 비활성화 되어는 `[Authorize]` 고 `[AllowAnonymous]` 특성은 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-225">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="5797d-226">IIS 사이트를 익명 액세스를 허용 하지 않도록 구성 된 경우 요청에 하지 앱에 도달 합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-226">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="5797d-227">이러한 이유로 `[AllowAnonymous]` 특성 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-227">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="5797d-228">익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="5797d-228">Allow anonymous access</span></span>

<span data-ttu-id="5797d-229">Windows 인증 및 익명 액세스를 모두 설정 된 경우 사용 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-229">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="5797d-230">`[Authorize]` 특성을 사용 하면 앱의 인증을 요구 하는 끝점을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-230">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="5797d-231">`[AllowAnonymous]` 재정의 특성의 `[Authorize]` 익명 액세스를 허용 하는 앱의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-231">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="5797d-232">특성 사용 세부 정보를 참조 하세요. <xref:security/authorization/simple>합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-232">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="5797d-233">기본적으로 권한 부여를 페이지에 액세스할 수 없는 사용자는 빈 HTTP 403 응답으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-233">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="5797d-234">합니다 [StatusCodePages 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) "액세스 거부" 더 나은 환경을 제공 하기 위해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-234">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="5797d-235">가장</span><span class="sxs-lookup"><span data-stu-id="5797d-235">Impersonation</span></span>

<span data-ttu-id="5797d-236">Asp.net 가장을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-236">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="5797d-237">앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱의 id를 사용 하 여 앱이 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-237">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="5797d-238">앱 사용자를 대신 하 여 작업을 수행 해야 하는 경우 사용 하 여 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 에 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 `Startup.Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-238">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="5797d-239">이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-239">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="5797d-240">`RunImpersonated` 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-240">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="5797d-241">예를 들어 전체 요청 또는 미들웨어 체인 래핑 지원 없거나 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-241">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5797d-242">동안 합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지를 사용 하면 Windows에서 인증, Linux 및 macOS의 경우 가장은 Windows 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-242">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="5797d-243">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="5797d-243">Claims transformations</span></span>

<span data-ttu-id="5797d-244">IIS에서 프로세스 모드를 사용 하 여 호스팅하는 경우 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-244">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5797d-245">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-245">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5797d-246">In process를 호스트 하는 경우 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은 참조 하세요. <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>합니다.</span><span class="sxs-lookup"><span data-stu-id="5797d-246">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5797d-247">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5797d-247">Additional resources</span></span>

* [<span data-ttu-id="5797d-248">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="5797d-248">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
