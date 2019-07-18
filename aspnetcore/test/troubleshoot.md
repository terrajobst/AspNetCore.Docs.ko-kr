---
title: ASP.NET Core 프로젝트 문제 해결
author: Rick-Anderson
description: ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: b434af2dd046045836d2f6f7f7b7b2d57699bedc
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308275"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="83a81-103">ASP.NET Core 프로젝트 문제 해결</span><span class="sxs-lookup"><span data-stu-id="83a81-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="83a81-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83a81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83a81-105">다음 링크는 문제 해결 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="83a81-106">NDC 회의 (런던, 2018): ASP.NET Core 응용 프로그램의 문제 진단</span><span class="sxs-lookup"><span data-stu-id="83a81-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="83a81-107">ASP.NET 블로그: ASP.NET Core 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="83a81-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="83a81-108">.NET Core SDK 경고</span><span class="sxs-lookup"><span data-stu-id="83a81-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="83a81-109">.NET Core SDK 32 비트 및 64 비트 버전이 모두 설치 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="83a81-110">ASP.NET Core에 대 한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="83a81-111">.NET Core SDK 32 비트 및 64 비트 버전이 모두 설치 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="83a81-112">' C:\\Program Files\\dotnet\\sdk\\'에 설치 된 64 비트 버전의 템플릿만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="83a81-113">이 경고는 32 비트 (x86) 및 64 비트 (x64) 버전의 [.NET Core SDK](https://www.microsoft.com/net/download/all) 모두 설치 된 경우에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="83a81-114">두 버전 모두 설치 될 수 있는 일반적인 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="83a81-115">원래 32 비트 컴퓨터를 사용 하 여 .NET Core SDK 설치 관리자를 다운로드 한 다음,이를 전체에 복사 하 고 64 비트 컴퓨터에 설치 했습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="83a81-116">32 비트 .NET Core SDK 다른 응용 프로그램에 의해 설치 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="83a81-117">잘못 된 버전이 다운로드 및 설치 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="83a81-118">이 경고를 방지 하려면 32 비트 .NET Core SDK를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="83a81-119">**제어판** > **프로그램 및**기능 > 에서 제거**프로그램을 제거 하거나 변경**합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="83a81-120">경고가 발생 하는 이유와 그에 대 한 영향을 이해 하는 경우 경고를 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="83a81-121">.NET Core SDK 여러 위치에 설치 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="83a81-122">ASP.NET Core에 대 한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="83a81-123">.NET Core SDK는 여러 위치에 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="83a81-124">' C:\\Program Files\\dotnet\\sdk\\'에 설치 된 sdk의 템플릿만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="83a81-125">하나 이상의.NET Core SDK 설치 디렉터리 외부에 있는 경우에이 메시지를 참조 하세요 *c:\\Program Files\\dotnet\\sdk\\* 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="83a81-126">일반적으로이는 .NET Core SDK MSI 설치 관리자 대신 복사/붙여넣기를 사용 하 여 컴퓨터에 배포 된 경우에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="83a81-127">이 경고를 방지 하려면 모든 32 비트 .NET Core Sdk 및 런타임을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="83a81-128">**제어판** > **프로그램 및**기능 > 에서 제거**프로그램을 제거 하거나 변경**합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="83a81-129">경고가 발생 하는 이유와 그에 대 한 영향을 이해 하는 경우 경고를 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="83a81-130">.NET Core Sdk가 검색 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="83a81-131">ASP.NET Core에 대 한 Visual Studio **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="83a81-132">.NET Core Sdk가 검색 되지 않았습니다. .NET Core Sdk가 환경 변수에 `PATH`포함 되어 있는지 확인 하세요.</span><span class="sxs-lookup"><span data-stu-id="83a81-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="83a81-133">`dotnet` 명령을 실행 하면 경고가 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="83a81-134">설치 된 dotnet Sdk를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="83a81-135">이러한 경고는 환경 변수가 `PATH` 컴퓨터의 .net Core sdk를 가리키지 않는 경우에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="83a81-136">이 문제를 해결 하려면 다음을 수행 하십시오.</span><span class="sxs-lookup"><span data-stu-id="83a81-136">To resolve this problem:</span></span>

* <span data-ttu-id="83a81-137">.NET Core SDK를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="83a81-138">[.Net 다운로드](https://dotnet.microsoft.com/download)에서 최신 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="83a81-139">환경 변수가 SDK가 설치 된 위치를 가리키는지 확인 합니다 (`C:\Program Files\dotnet\` 64 비트/x64의 경우 또는 `C:\Program Files (x86)\dotnet\` 32 비트/x 86의 경우). `PATH`</span><span class="sxs-lookup"><span data-stu-id="83a81-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="83a81-140">SDK 설치 관리자는 일반적으로 `PATH`를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="83a81-141">항상 동일한 비트 Sdk 및 런타임을 동일한 컴퓨터에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="83a81-142">.NET Core 호스팅 번들을 설치한 후 SDK가 누락 됨</span><span class="sxs-lookup"><span data-stu-id="83a81-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="83a81-143">.Net core [호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) 을 설치 하면 .net `PATH` core ()의 32 비트 (x86) 버전을 가리키도록 .net core 런타임을 설치할 때이 (`C:\Program Files (x86)\dotnet\`가) 수정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="83a81-144">이로 인해 32 비트 (x86) .net core `dotnet` 명령이 사용 될 때 sdk가 누락 될 수 있습니다 ([.net core sdk가 검색 되지 않음](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="83a81-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="83a81-145">이 문제를 해결 하려면 앞 `C:\Program Files\dotnet\` `C:\Program Files (x86)\dotnet\` `PATH`의 위치로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="83a81-146">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="83a81-146">Obtain data from an app</span></span>

<span data-ttu-id="83a81-147">앱이 요청에 응답할 수 있는 경우 미들웨어를 사용 하 여 앱에서 다음 데이터를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="83a81-148">요청 &ndash; 메서드, 스키마, 호스트, pathbase, 경로, 쿼리 문자열, 헤더</span><span class="sxs-lookup"><span data-stu-id="83a81-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="83a81-149">연결 &ndash; 원격 IP 주소, 원격 포트, 로컬 IP 주소, 로컬 포트, 클라이언트 인증서</span><span class="sxs-lookup"><span data-stu-id="83a81-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="83a81-150">Id &ndash; 이름, 표시 이름</span><span class="sxs-lookup"><span data-stu-id="83a81-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="83a81-151">구성 설정</span><span class="sxs-lookup"><span data-stu-id="83a81-151">Configuration settings</span></span>
* <span data-ttu-id="83a81-152">환경 변수</span><span class="sxs-lookup"><span data-stu-id="83a81-152">Environment variables</span></span>

<span data-ttu-id="83a81-153">`Startup.Configure` 메서드의 요청 처리 파이프라인의 시작 부분에 다음 [미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="83a81-154">개발 환경 에서만 코드가 실행 되도록 하기 위해 미들웨어가 실행 되기 전에 환경이 검사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="83a81-155">환경을 얻으려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="83a81-156">`IHostingEnvironment` 을`Startup.Configure` 메서드에 삽입 하 고 지역 변수를 사용 하 여 환경을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="83a81-157">다음 샘플 코드에서는이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="83a81-158">`Startup` 클래스의 속성에 환경을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="83a81-159">속성 (예:)을 `if (Environment.IsDevelopment())`사용 하 여 환경을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="83a81-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```
