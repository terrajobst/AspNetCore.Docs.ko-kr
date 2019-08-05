---
title: HTTP REPL을 사용하여 웹 API 테스트
author: scottaddie
description: HTTP REPL .NET Core 전역 도구를 사용하여 ASP.NET Core 웹 API를 찾아보고 테스트하는 방법에 대해 알아보세요.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: e719d599545810d723840b0800cd6a2b4f96b123
ms.sourcegitcommit: fbc66827e319d28bebed678ea5fd42f582fe3c34
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493580"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="5aa2b-103">HTTP REPL을 사용하여 웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="5aa2b-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="5aa2b-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="5aa2b-105">HTTP REPL(Read-Eval-Print Loop):</span><span class="sxs-lookup"><span data-stu-id="5aa2b-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="5aa2b-106">.NET Core가 지원되는 모든 곳에서 경량 플랫폼 간 명령줄 도구가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="5aa2b-107">ASP.NET Core 웹 API(및 non-ASP.NET Core 웹 API)를 테스트하고 결과를 확인하는 HTTP 요청을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="5aa2b-108">로컬 호스트, Azure App Service를 포함하여 모든 환경에 호스팅된 웹 API를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="5aa2b-109">지원되는 [HTTP 동사](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="5aa2b-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="5aa2b-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="5aa2b-111">GET</span><span class="sxs-lookup"><span data-stu-id="5aa2b-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="5aa2b-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="5aa2b-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="5aa2b-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="5aa2b-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="5aa2b-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="5aa2b-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="5aa2b-115">POST</span><span class="sxs-lookup"><span data-stu-id="5aa2b-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="5aa2b-116">PUT</span><span class="sxs-lookup"><span data-stu-id="5aa2b-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="5aa2b-117">본 내용을 따라가려면 [샘플 ASP.NET Core 웹 API를 보거나 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)([다운로드 방법](xref:index#how-to-download-a-sample))하세요.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5aa2b-118">전제 조건</span><span class="sxs-lookup"><span data-stu-id="5aa2b-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="5aa2b-119">설치</span><span class="sxs-lookup"><span data-stu-id="5aa2b-119">Installation</span></span>

<span data-ttu-id="5aa2b-120">HTTP REPL을 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="5aa2b-121">[.NET Core 글로벌 도구](/dotnet/core/tools/global-tools#install-a-global-tool)가 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 패키지에서 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="5aa2b-122">사용법</span><span class="sxs-lookup"><span data-stu-id="5aa2b-122">Usage</span></span>

<span data-ttu-id="5aa2b-123">도구를 성공적으로 설치한 후 다음 명령을 실행하여 HTTP REPL을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="5aa2b-124">사용 가능한 HTTP REPL 명령을 보려면 다음 명령 중 하나를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="5aa2b-125">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-125">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Sets the swagger document to use for information about the current server
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="5aa2b-126">HTTP REPL은 명령 완성을 제안합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="5aa2b-127"><kbd>Tab</kbd> 키를 누르면 입력한 문자 또는 API 엔드포인트를 완성하는 명령 목록이 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="5aa2b-128">다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="5aa2b-129">웹 API에 연결</span><span class="sxs-lookup"><span data-stu-id="5aa2b-129">Connect to the web API</span></span>

<span data-ttu-id="5aa2b-130">다음 명령을 실행하여 웹 API에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="5aa2b-131">`<BASE URI>`는 웹 API의 기본 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-131">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="5aa2b-132">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="5aa2b-133">또는 HTTP REPL이 실행되는 동안 언제든지 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="5aa2b-134">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-134">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="5aa2b-135">웹 API에 대한 Swagger 문서를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-135">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="5aa2b-136">웹 API를 제대로 검사하려면 웹 API에 대한 Swagger 문서에 관련 URI를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-136">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="5aa2b-137">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-137">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="5aa2b-138">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-138">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="5aa2b-139">웹 API 탐색</span><span class="sxs-lookup"><span data-stu-id="5aa2b-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="5aa2b-140">사용 가능한 엔드포인트 보기</span><span class="sxs-lookup"><span data-stu-id="5aa2b-140">View available endpoints</span></span>

<span data-ttu-id="5aa2b-141">웹 API 주소의 현재 경로에 있는 다른 엔드포인트(컨트롤러)를 나열하려면 `ls` 또는 `dir` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="5aa2b-142">다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="5aa2b-143">위의 출력은 사용 가능한 두 컨트롤러 `Fruits` 및 `People`을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="5aa2b-144">두 컨트롤러 모두 매개 변수 없는 HTTP GET 및 POST 작업을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="5aa2b-145">특정 컨트롤러로 이동하면 자세한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="5aa2b-146">예를 들어 다음 명령의 출력은 `Fruits` 컨트롤러에서 HTTP GET, PUT 및 DELETE 작업도 지원함을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="5aa2b-147">각 작업의 경로에는 `id` 매개 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="5aa2b-148">아니면 `ui` 명령을 실행하여 브라우저에서 웹 API의 Swagger UI 페이지를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="5aa2b-149">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="5aa2b-150">엔드포인트로 이동</span><span class="sxs-lookup"><span data-stu-id="5aa2b-150">Navigate to an endpoint</span></span>

<span data-ttu-id="5aa2b-151">웹 API에서 다른 엔드포인트로 이동하려면 `cd` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="5aa2b-152">`cd` 명령 다음의 경로는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="5aa2b-153">다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="5aa2b-154">HTTP REPL 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="5aa2b-155">HTTP REPL의 기본 [색](#set-color-preferences)을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="5aa2b-156">또한 [기본 텍스트 편집기](#set-the-default-text-editor)를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="5aa2b-157">HTTP REPL 기본 설정은 현재 세션에서 유지되고 이후 세션에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="5aa2b-158">기본 설정을 수정하는 경우 다음 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="5aa2b-159">Linux</span><span class="sxs-lookup"><span data-stu-id="5aa2b-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="5aa2b-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="5aa2b-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="5aa2b-161">macOS</span><span class="sxs-lookup"><span data-stu-id="5aa2b-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="5aa2b-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="5aa2b-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="5aa2b-163">Windows</span><span class="sxs-lookup"><span data-stu-id="5aa2b-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="5aa2b-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="5aa2b-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="5aa2b-165">*Httpreplprefs* 파일은 시작할 때 로드되며 런타임 시 변경 내용에 대한 모니터링이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="5aa2b-166">파일에 직접 수정한 내용은 도구를 다시 시작한 후에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="5aa2b-167">설정 보기</span><span class="sxs-lookup"><span data-stu-id="5aa2b-167">View the settings</span></span>

<span data-ttu-id="5aa2b-168">사용 가능한 설정을 보려면 `pref get` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="5aa2b-169">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="5aa2b-170">위의 명령은 사용 가능한 키-값 쌍을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="5aa2b-171">색 기본 설정 지정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-171">Set color preferences</span></span>

<span data-ttu-id="5aa2b-172">응답 색 지정은 현재 JSON에만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="5aa2b-173">기본 HTTP REPL 도구 색 지정을 사용자 지정하려면 변경할 색에 해당하는 키를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="5aa2b-174">키를 찾는 방법에 대한 지침은 [설정 보기](#view-the-settings) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="5aa2b-175">예를 들어 다음과 같이 `colors.json` 키 값을 `Green`에서 `White`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="5aa2b-176">[허용되는 색](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="5aa2b-177">이후의 HTTP 요청은 새 색 지정을 사용하여 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="5aa2b-178">특정 색 키가 설정되지 않은 경우에는 보다 일반적인 키가 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="5aa2b-179">이 대체 동작을 보여 주려면 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="5aa2b-180">`colors.json.name`에 값이 없으면 `colors.json.string`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="5aa2b-181">`colors.json.string`에 값이 없으면 `colors.json.literal`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="5aa2b-182">`colors.json.literal`에 값이 없으면 `colors.json`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="5aa2b-183">`colors.json`에 값이 없으면 명령 셸의 기본 텍스트 색(`AllowedColors.None`)이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="5aa2b-184">들여쓰기 크기 설정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-184">Set indentation size</span></span>

<span data-ttu-id="5aa2b-185">응답 들여쓰기 크기 사용자 지정은 현재 JSON에 대해서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="5aa2b-186">기본 크기는 두 개의 공백입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-186">The default size is two spaces.</span></span> <span data-ttu-id="5aa2b-187">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="5aa2b-188">기본 크기를 변경하려면 `formatting.json.indentSize` 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="5aa2b-189">예를 들어 항상 네 개의 공백을 사용하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="5aa2b-190">후속 응답은 네 개의 공백 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-indentation-size"></a><span data-ttu-id="5aa2b-191">들여쓰기 크기 설정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-191">Set indentation size</span></span>

<span data-ttu-id="5aa2b-192">응답 들여쓰기 크기 사용자 지정은 현재 JSON에 대해서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="5aa2b-193">기본 크기는 두 개의 공백입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-193">The default size is two spaces.</span></span> <span data-ttu-id="5aa2b-194">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-194">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="5aa2b-195">기본 크기를 변경하려면 `formatting.json.indentSize` 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="5aa2b-196">예를 들어 항상 네 개의 공백을 사용하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="5aa2b-197">후속 응답은 네 개의 공백 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-197">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="5aa2b-198">기본 텍스트 편집기 설정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-198">Set the default text editor</span></span>

<span data-ttu-id="5aa2b-199">기본적으로 HTTP REPL에는 사용하도록 구성된 텍스트 편집기가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="5aa2b-200">HTTP 요청 본문이 필요한 웹 API 메서드를 테스트하려면 기본 텍스트 편집기를 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="5aa2b-201">HTTP REPL 도구는 요청 본문을 작성할 목적으로만 구성된 텍스트 편집기를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="5aa2b-202">다음 명령을 실행하여 기본 설정 텍스트 편집기를 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="5aa2b-203">앞의 명령에서는 `<EXECUTABLE>`이 텍스트 편집기 실행 파일의 전체 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="5aa2b-204">예를 들어 다음 명령을 실행하여 Visual Studio Code를 기본 텍스트 편집기로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="5aa2b-205">Linux</span><span class="sxs-lookup"><span data-stu-id="5aa2b-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="5aa2b-206">macOS</span><span class="sxs-lookup"><span data-stu-id="5aa2b-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="5aa2b-207">Windows</span><span class="sxs-lookup"><span data-stu-id="5aa2b-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="5aa2b-208">특정 CLI 인수를 사용하여 기본 텍스트 편집기를 시작하려면 `editor.command.default.arguments` 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="5aa2b-209">예를 들어 Visual Studio Code가 기본 텍스트 편집기이며 항상 HTTP REPL이 확장을 사용하지 않도록 설정한 새 세션에서 Visual Studio Code를 열도록 하는 경우를 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="5aa2b-210">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="5aa2b-211">HTTP GET 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-211">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-212">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-212">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-213">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-213">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-214">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-214">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-215">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-215">Options</span></span>

<span data-ttu-id="5aa2b-216">다음 옵션은 `get` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-216">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="5aa2b-217">예</span><span class="sxs-lookup"><span data-stu-id="5aa2b-217">Example</span></span>

<span data-ttu-id="5aa2b-218">HTTP GET 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-218">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="5aa2b-219">이를 지원하는 엔드포인트에서 `get` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-219">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="5aa2b-220">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-220">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="5aa2b-221">`get` 명령에 매개 변수를 전달하여 특정 레코드를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-221">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="5aa2b-222">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-222">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="5aa2b-223">HTTP POST 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-223">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-224">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-224">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-225">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-225">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-226">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-226">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-227">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-227">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="5aa2b-228">예</span><span class="sxs-lookup"><span data-stu-id="5aa2b-228">Example</span></span>

<span data-ttu-id="5aa2b-229">HTTP POST 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-229">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="5aa2b-230">이를 지원하는 엔드포인트에서 `post` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-230">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="5aa2b-231">앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-231">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="5aa2b-232">기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-232">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="5aa2b-233">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-233">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="5aa2b-234">기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-234">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="5aa2b-235">모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-235">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="5aa2b-236">*.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-236">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="5aa2b-237">명령 셸에 다음 출력이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-237">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="5aa2b-238">HTTP PUT 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-238">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-239">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-239">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-240">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-240">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-241">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-241">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-242">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-242">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="5aa2b-243">예</span><span class="sxs-lookup"><span data-stu-id="5aa2b-243">Example</span></span>

<span data-ttu-id="5aa2b-244">HTTP PUT 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-244">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="5aa2b-245">*선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-245">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="5aa2b-246">앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-246">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="5aa2b-247">기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-247">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="5aa2b-248">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-248">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="5aa2b-249">기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-249">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="5aa2b-250">모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-250">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="5aa2b-251">*.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-251">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="5aa2b-252">명령 셸에 다음 출력이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-252">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="5aa2b-253">*선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-253">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="5aa2b-254">예를 들어 텍스트 편집기에서 “Cherry”를 입력한 경우 `get`은 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-254">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="5aa2b-255">HTTP DELETE 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-255">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-256">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-256">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-257">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-258">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-259">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="5aa2b-260">예</span><span class="sxs-lookup"><span data-stu-id="5aa2b-260">Example</span></span>

<span data-ttu-id="5aa2b-261">HTTP DELETE 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-261">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="5aa2b-262">*선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="5aa2b-263">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-263">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="5aa2b-264">*선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="5aa2b-265">이 예제에서 `get`은 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-265">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="5aa2b-266">HTTP PATCH 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-266">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-267">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-267">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-268">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-269">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-270">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="5aa2b-271">HTTP HEAD 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-271">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-272">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-272">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-273">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-273">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-274">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-274">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-275">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-275">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="5aa2b-276">HTTP OPTIONS 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="5aa2b-276">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="5aa2b-277">개요</span><span class="sxs-lookup"><span data-stu-id="5aa2b-277">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="5aa2b-278">인수</span><span class="sxs-lookup"><span data-stu-id="5aa2b-278">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="5aa2b-279">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-279">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="5aa2b-280">옵션</span><span class="sxs-lookup"><span data-stu-id="5aa2b-280">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="5aa2b-281">HTTP 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="5aa2b-281">Set HTTP request headers</span></span>

<span data-ttu-id="5aa2b-282">HTTP 요청 헤더를 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-282">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="5aa2b-283">HTTP 요청을 사용하여 인라인으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-283">Set inline with the HTTP request.</span></span> <span data-ttu-id="5aa2b-284">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-284">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="5aa2b-285">앞의 방법을 사용하는 경우 개별 HTTP 요청 헤더에는 자체 `-h` 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-285">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="5aa2b-286">HTTP 요청을 보내기 전에 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-286">Set before sending the HTTP request.</span></span> <span data-ttu-id="5aa2b-287">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-287">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="5aa2b-288">요청을 보내기 전에 헤더를 설정하는 경우 헤더는 명령 셸 세션 기간에 설정된 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-288">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="5aa2b-289">헤더를 지우려면 빈 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-289">To clear the header, provide an empty value.</span></span> <span data-ttu-id="5aa2b-290">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-290">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="5aa2b-291">HTTP 요청 표시 토글</span><span class="sxs-lookup"><span data-stu-id="5aa2b-291">Toggle HTTP request display</span></span>

<span data-ttu-id="5aa2b-292">기본적으로 보내는 HTTP 요청 표시는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-292">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="5aa2b-293">명령 셸 세션 기간에 해당하는 설정을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-293">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="5aa2b-294">요청 표시 사용</span><span class="sxs-lookup"><span data-stu-id="5aa2b-294">Enable request display</span></span>

<span data-ttu-id="5aa2b-295">`echo on` 명령을 실행하여 보내는 HTTP 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-295">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="5aa2b-296">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-296">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="5aa2b-297">현재 세션의 후속 HTTP 요청은 요청 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-297">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="5aa2b-298">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-298">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="5aa2b-299">요청 표시 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="5aa2b-299">Disable request display</span></span>

<span data-ttu-id="5aa2b-300">`echo off` 명령을 실행하여 보내는 HTTP 요청을 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-300">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="5aa2b-301">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-301">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="5aa2b-302">스크립트 실행</span><span class="sxs-lookup"><span data-stu-id="5aa2b-302">Run a script</span></span>

<span data-ttu-id="5aa2b-303">동일한 HTTP REPL 명령 세트를 자주 실행하는 경우에는 텍스트 파일에 저장하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-303">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="5aa2b-304">파일의 명령은 명령줄에서 수동으로 실행할 때와 동일한 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-304">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="5aa2b-305">`run` 명령을 사용하여 일괄적으로 명령을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-305">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="5aa2b-306">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-306">For example:</span></span>

1. <span data-ttu-id="5aa2b-307">줄 바꿈 기호로 분리된 명령 세트를 포함하는 텍스트 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-307">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="5aa2b-308">다음 명령을 포함하는 *people-script.txt* 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-308">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="5aa2b-309">텍스트 파일의 경로를 전달하여 `run` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-309">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="5aa2b-310">예:</span><span class="sxs-lookup"><span data-stu-id="5aa2b-310">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="5aa2b-311">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-311">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="5aa2b-312">출력 지우기</span><span class="sxs-lookup"><span data-stu-id="5aa2b-312">Clear the output</span></span>

<span data-ttu-id="5aa2b-313">HTTP REPL 도구에 의해 명령 셸에 작성된 모든 출력을 제거하려면 `clear` 또는 `cls` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-313">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="5aa2b-314">설명을 위해 명령 셸에 다음 출력이 포함되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-314">To illustrate, imagine the command shell contains the following output:</span></span>

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="5aa2b-315">다음 명령을 실행하여 출력을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-315">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="5aa2b-316">앞의 명령을 실행한 후 명령 셸은 다음 출력만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5aa2b-316">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="5aa2b-317">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5aa2b-317">Additional resources</span></span>

* [<span data-ttu-id="5aa2b-318">REST API 요청</span><span class="sxs-lookup"><span data-stu-id="5aa2b-318">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="5aa2b-319">HTTP REPL GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="5aa2b-319">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
