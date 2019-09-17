---
title: TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용
author: ssougnez
description: 이 자습서에서는 해당 클라이언트가 TypeScript로 작성된 ASP.NET Core SignalR 웹앱을 번들링 및 빌드하도록 WebPack을 구성합니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 04/23/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 628fbb9940ad14cb15e3abd88b8b6a524b24d70a
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773956"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="da80b-103">TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용</span><span class="sxs-lookup"><span data-stu-id="da80b-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="da80b-104">작성자: [Sébastien Sougnez](https://twitter.com/ssougnez) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="da80b-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="da80b-105">[WebPack](https://webpack.js.org/)을 사용하여 개발자가 웹앱의 클라이언트 쪽 리소스를 번들링 및 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="da80b-106">본 자습서에서는 클라이언트가 [TypeScript](https://www.typescriptlang.org/)로 작성된 ASP.NET Core SignalR 웹앱에서 WebPack을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="da80b-107">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="da80b-108">ASP.NET Core SignalR 앱 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="da80b-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="da80b-109">SignalR TypeScript 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="da80b-110">WebPack을 사용하여 빌드 파이프라인 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="da80b-111">SignalR 서버 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="da80b-112">클라이언트 및 서버 간 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="da80b-112">Enable communication between client and server</span></span>

<span data-ttu-id="da80b-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da80b-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="da80b-114">전제 조건</span><span class="sxs-lookup"><span data-stu-id="da80b-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da80b-116">**ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="da80b-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="da80b-117">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="da80b-118">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="da80b-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="da80b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="da80b-121">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="da80b-122">Visual Studio Code용 C# 버전 1.17.1 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="da80b-123">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="da80b-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="da80b-124">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="da80b-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da80b-126">*PATH* 환경 변수에서 npm을 찾도록 Visual Studio를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="da80b-127">기본적으로 Visual Studio는 설치 디렉터리에 있는 npm의 버전을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="da80b-128">Visual Studio에서 다음 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="da80b-129">**도구** > **옵션** > **프로젝트 및 솔루션** > **웹 패키지 관리** > **외부 웹 도구**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="da80b-130">목록에서 *$(PATH)* 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="da80b-131">위쪽 화살표를 클릭하여 이 항목을 목록의 두 번째 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 구성](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="da80b-133">Visual Studio 구성이 완료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="da80b-134">이제 프로젝트를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-134">It's time to create the project.</span></span>

1. <span data-ttu-id="da80b-135">**파일** > **새로 만들기** > **프로젝트** 메뉴 옵션에서 **ASP.NET Core 웹 애플리케이션** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="da80b-136">프로젝트 이름을 *SignalRWebPack*으로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="da80b-137">대상 프레임워크 드롭다운에서 *.NET Core*를 선택하고, 프레임워크 선택기 드롭다운에서 *ASP.NET Core 3.0*을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="da80b-138">**빈** 템플릿을 선택하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="da80b-140">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-140">Run the following command in the **Integrated Terminal**:</span></span>

```console
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="da80b-141">.NET Core를 대상으로 하는 빈 ASP.NET Core 웹앱이 *SignalRWebPack* 디렉터리에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="da80b-142">WebPack 및 TypeScript 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="da80b-143">다음 단계에서는 TypeScript에서 JavaScript로 변환 및 클라이언트 쪽 리소스의 번들링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="da80b-144">프로젝트 루트에서 다음 명령을 실행하여 *package.json* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="da80b-145">강조 표시된 속성을 *package.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="da80b-146">`private` 속성을 `true`로 설정하면 다음 단계에서 패키지 설치 경고가 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="da80b-147">필요한 npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-147">Install the required npm packages.</span></span> <span data-ttu-id="da80b-148">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="da80b-149">참고할 몇몇 명령 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="da80b-149">Some command details to note:</span></span>

    * <span data-ttu-id="da80b-150">버전 번호는 각 패키지 이름에 대해 `@` 부호 뒤에 옵니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="da80b-151">npm은 해당 특정 패키지 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="da80b-152">`-E` 옵션은 [유의적 버전](https://semver.org/) 범위 연산자를 *package.json*에 쓰는 npm의 기본 동작을 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="da80b-153">예를 들어 `"webpack": "^4.29.3"` 대신 `"webpack": "4.29.3"`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="da80b-154">이 옵션은 최신 패키지 버전으로 의도하지 않은 업그레이드를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="da80b-155">자세한 내용은 [npm-install](https://docs.npmjs.com/cli/install) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da80b-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="da80b-156">*package.json* 파일의 `scripts` 속성을 다음 코드 조각으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="da80b-157">스크립트에 대한 간략한 설명:</span><span class="sxs-lookup"><span data-stu-id="da80b-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="da80b-158">`build`: 개발 모드에서 클라이언트 쪽 리소스를 번들링하고 파일 변경을 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="da80b-159">파일 감시자는 프로젝트 파일이 변경될 때마다 번들이 다시 생성되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="da80b-160">`mode` 옵션은 프로덕션 최적화(예: 트리 셰이킹 및 축소)를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="da80b-161">개발 시에는 `build`만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="da80b-162">`release`: 프로덕션 모드에서 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="da80b-163">`publish`: 프로덕션 모드에서 `release` 스크립트를 실행하여 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="da80b-164">이는 .NET Core CLI의 [publish](/dotnet/core/tools/dotnet-publish) 명령을 호출하여 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="da80b-165">프로젝트 루트에 다음 내용을 포함한 *webpack.config.js*라는 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="da80b-166">앞의 파일은 WebPack 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="da80b-167">참고할 일부 구성 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="da80b-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="da80b-168">`output` 속성은 *dist*의 기본값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="da80b-169">번들은 *wwwroot* 디렉터리에 대신 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="da80b-170">`resolve.extensions` 배열은 SignalR 클라이언트 JavaScript를 가져오기 위한 *.js*를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="da80b-171">프로젝트 루트에 새 *src* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="da80b-172">그 목적은 프로젝트의 클라이언트 쪽 자산을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="da80b-173">다음 내용을 포함한 *src/index.html*을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="da80b-174">앞의 HTML은 홈 페이지의 상용구 마크업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="da80b-175">새 *src/css* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="da80b-176">그 목적은 프로젝트의 *.css* 파일을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="da80b-177">다음 내용을 포함한 *src/css/main.css*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="da80b-178">앞의 *main.css* 파일은 앱의 스타일을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="da80b-179">다음 내용을 포함한 *src/tsconfig.json*을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="da80b-180">앞의 코드는 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 호환 JavaScript를 생성하도록 TypeScript 컴파일러를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="da80b-181">다음 내용을 포함한 *src/index.ts*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="da80b-182">이 TypeScript는 DOM 요소 참조를 조회하여 두 가지 이벤트 핸들러를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="da80b-183">`keyup`: 이 이벤트는 사용자가 `tbMessage`로 식별되는 텍스트 상자에 내용을 입력할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="da80b-184">`send` 함수는 사용자가 **Enter** 키를 누를 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="da80b-185">`click`: 이 이벤트는 사용자가 **보내기** 단추를 클릭할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="da80b-186">`send` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="da80b-187">ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="da80b-188">`Startup.Configure` 메서드에서 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 및 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)에 대한 호출을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="da80b-189">이 코드는 사용자가 전체 URL을 입력하는지 아니면 웹앱의 루트 URL만 입력하는지 관계없이 서버가 *index.html* 파일을 찾아서 제공할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="da80b-190">`Startup.Configure` 메서드 끝에서 */hub* 경로를 `ChatHub` 허브에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="da80b-191">*Hello World!* 를 표시하는 코드를</span><span class="sxs-lookup"><span data-stu-id="da80b-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="da80b-192">다음 줄로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="da80b-193">`Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="da80b-194">이는 SignalR 서비스를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="da80b-195">프로젝트 루트에 *Hubs*라는 새 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="da80b-196">그 목적은 다음 단계에서 생성되는 SignalR 허브를 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="da80b-197">다음 내용을 포함한 허브 *Hubs/ChatHub.cs*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="da80b-198">`ChatHub` 참조를 확인하기 위해 *Startup.cs* 파일의 맨 위에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="da80b-199">클라이언트 및 서버 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="da80b-199">Enable client and server communication</span></span>

<span data-ttu-id="da80b-200">앱은 현재 메시지를 보낼 간단한 양식을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="da80b-201">그런 시도를 할 때 아무 일도 일어나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="da80b-202">서버는 특정 경로를 수신 대기하지만 보낸 메시지를 사용하여 아무 일도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="da80b-203">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="da80b-204">앞의 명령은 클라이언트가 서버에 메시지를 보낼 수 있도록 하는 [SignalR TypeScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="da80b-205">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="da80b-206">앞의 코드는 서버에서 오는 메시지의 수신을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="da80b-207">`HubConnectionBuilder` 클래스는 서버 연결을 구성하기 위한 새 빌더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="da80b-208">`withUrl` 함수는 허브 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-208">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="da80b-209">SignalR은 클라이언트 및 서버 간 메시지 교환을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-209">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="da80b-210">각 메시지는 특정 이름을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-210">Each message has a specific name.</span></span> <span data-ttu-id="da80b-211">예를 들어 메시지 영역에 새 메시지를 표시하는 로직을 실행하는 `messageReceived`라는 이름을 가진 메시지가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="da80b-212">특정 메시지 수신 대기는 `on` 함수를 통해 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="da80b-213">임의 개수의 메시지 이름을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-213">You can listen to any number of message names.</span></span> <span data-ttu-id="da80b-214">또한 수신 메시지의 작성자 이름 및 내용 등을 메시지에 파라미터로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="da80b-215">클라이언트가 메시지를 수신한 후 `innerHTML` 특성의 작성자 이름 및 메시지 내용을 사용하여 새 `div` 요소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="da80b-216">이 요소가 주 `div` 요소에 추가되어 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="da80b-217">이제 클라이언트가 메시지를 수신할 수 있으므로, 메시지를 보내도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="da80b-218">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="da80b-219">WebSockets 연결을 통해 메시지를 보내면 `send` 메서드를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="da80b-220">이 메서드의 첫 번째 매개 변수는 메시지의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="da80b-221">메시지의 데이터는 다른 매개 변수를 통해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="da80b-222">이 예제에서는 `newMessage`로 식별되는 메시지가 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="da80b-223">메시지는 사용자 이름 및 텍스트 상자에 입력된 사용자 입력으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="da80b-224">보내기가 작동하면 텍스트 상자 값이 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="da80b-225">강조 표시된 메서드를 `ChatHub` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="da80b-226">앞의 코드는 서버가 메시지를 수신한 후 수신된 메시지를 모든 연결된 사용자에게 브로드캐스트합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="da80b-227">모든 메시지를 수신하기 위해 일반 `on` 메서드를 포함할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="da80b-228">메시지 이름 뒤에 명명한 메서드로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="da80b-229">이 예에서 TypeScript 클라이언트는 `newMessage`로 식별된 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="da80b-230">C# `NewMessage` 메서드에는 클라이언트가 보낸 데이터가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="da80b-231">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)에 대한 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="da80b-232">수신된 메시지를 허브에 연결된 모든 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="da80b-233">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="da80b-233">Test the app</span></span>

<span data-ttu-id="da80b-234">다음 단계를 사용하여 앱이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="da80b-236">WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="da80b-237">**패키지 관리자 콘솔** 창을 사용하여 프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="da80b-238">프로젝트 루트가 아닌 경우 명령을 입력하기 전에 `cd SignalRWebPack`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="da80b-239">**디버그** > **디버그하지 않고 시작**을 선택하여 디버거를 연결하지 않고 브라우저에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="da80b-240">`http://localhost:<port_number>`에서 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="da80b-241">컴파일 오류가 발생하면 솔루션을 닫았다가 다시 열어 보세요.</span><span class="sxs-lookup"><span data-stu-id="da80b-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="da80b-242">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="da80b-243">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="da80b-244">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="da80b-245">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="da80b-247">프로젝트 루트에서 다음 명령을 실행하여 WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="da80b-248">프로젝트 루트에서 다음 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-248">Build and run the app by executing the following command in the project root:</span></span>

    ```console
    dotnet run
    ```

    <span data-ttu-id="da80b-249">웹 서버가 앱을 시작하고 로컬 호스트에서 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="da80b-250">`http://localhost:<port_number>`에 대해 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="da80b-251">그러면 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="da80b-252">주소 표시줄에서 URL을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="da80b-253">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="da80b-254">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="da80b-255">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="da80b-256">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![두 브라우저 창 모두에 표시된 메시지](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-258">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da80b-259">**ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="da80b-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="da80b-260">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-260">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="da80b-261">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="da80b-261">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="da80b-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-263">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="da80b-264">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-264">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="da80b-265">Visual Studio Code용 C# 버전 1.17.1 이상</span><span class="sxs-lookup"><span data-stu-id="da80b-265">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="da80b-266">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="da80b-266">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="da80b-267">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="da80b-267">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-268">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da80b-269">*PATH* 환경 변수에서 npm을 찾도록 Visual Studio를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-269">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="da80b-270">기본적으로 Visual Studio는 설치 디렉터리에 있는 npm의 버전을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-270">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="da80b-271">Visual Studio에서 다음 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-271">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="da80b-272">**도구** > **옵션** > **프로젝트 및 솔루션** > **웹 패키지 관리** > **외부 웹 도구**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-272">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="da80b-273">목록에서 *$(PATH)* 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-273">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="da80b-274">위쪽 화살표를 클릭하여 이 항목을 목록의 두 번째 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-274">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 구성](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="da80b-276">Visual Studio 구성이 완료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-276">Visual Studio configuration is completed.</span></span> <span data-ttu-id="da80b-277">이제 프로젝트를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-277">It's time to create the project.</span></span>

1. <span data-ttu-id="da80b-278">**파일** > **새로 만들기** > **프로젝트** 메뉴 옵션에서 **ASP.NET Core 웹 애플리케이션** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-278">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="da80b-279">프로젝트 이름을 *SignalRWebPack*으로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-279">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="da80b-280">대상 프레임워크 드롭다운에서 *.NET Core*를 선택하고, 프레임워크 선택기 드롭다운에서 *ASP.NET Core 2.2*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-280">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="da80b-281">**빈** 템플릿을 선택하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-281">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="da80b-283">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-283">Run the following command in the **Integrated Terminal**:</span></span>

```console
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="da80b-284">.NET Core를 대상으로 하는 빈 ASP.NET Core 웹앱이 *SignalRWebPack* 디렉터리에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-284">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="da80b-285">WebPack 및 TypeScript 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-285">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="da80b-286">다음 단계에서는 TypeScript에서 JavaScript로 변환 및 클라이언트 쪽 리소스의 번들링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-286">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="da80b-287">프로젝트 루트에서 다음 명령을 실행하여 *package.json* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-287">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="da80b-288">강조 표시된 속성을 *package.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-288">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="da80b-289">`private` 속성을 `true`로 설정하면 다음 단계에서 패키지 설치 경고가 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-289">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="da80b-290">필요한 npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-290">Install the required npm packages.</span></span> <span data-ttu-id="da80b-291">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-291">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="da80b-292">참고할 몇몇 명령 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="da80b-292">Some command details to note:</span></span>

    * <span data-ttu-id="da80b-293">버전 번호는 각 패키지 이름에 대해 `@` 부호 뒤에 옵니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-293">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="da80b-294">npm은 해당 특정 패키지 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-294">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="da80b-295">`-E` 옵션은 [유의적 버전](https://semver.org/) 범위 연산자를 *package.json*에 쓰는 npm의 기본 동작을 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-295">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="da80b-296">예를 들어 `"webpack": "^4.29.3"` 대신 `"webpack": "4.29.3"`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-296">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="da80b-297">이 옵션은 최신 패키지 버전으로 의도하지 않은 업그레이드를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-297">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="da80b-298">자세한 내용은 [npm-install](https://docs.npmjs.com/cli/install) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da80b-298">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="da80b-299">*package.json* 파일의 `scripts` 속성을 다음 코드 조각으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-299">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="da80b-300">스크립트에 대한 간략한 설명:</span><span class="sxs-lookup"><span data-stu-id="da80b-300">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="da80b-301">`build`: 개발 모드에서 클라이언트 쪽 리소스를 번들링하고 파일 변경을 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-301">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="da80b-302">파일 감시자는 프로젝트 파일이 변경될 때마다 번들이 다시 생성되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-302">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="da80b-303">`mode` 옵션은 프로덕션 최적화(예: 트리 셰이킹 및 축소)를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-303">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="da80b-304">개발 시에는 `build`만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-304">Only use `build` in development.</span></span>
    * <span data-ttu-id="da80b-305">`release`: 프로덕션 모드에서 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-305">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="da80b-306">`publish`: 프로덕션 모드에서 `release` 스크립트를 실행하여 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-306">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="da80b-307">이는 .NET Core CLI의 [publish](/dotnet/core/tools/dotnet-publish) 명령을 호출하여 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-307">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="da80b-308">프로젝트 루트에 다음 내용을 포함한 *webpack.config.js*라는 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-308">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="da80b-309">앞의 파일은 WebPack 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-309">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="da80b-310">참고할 일부 구성 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="da80b-310">Some configuration details to note:</span></span>

    * <span data-ttu-id="da80b-311">`output` 속성은 *dist*의 기본값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-311">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="da80b-312">번들은 *wwwroot* 디렉터리에 대신 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-312">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="da80b-313">`resolve.extensions` 배열은 SignalR 클라이언트 JavaScript를 가져오기 위한 *.js*를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-313">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="da80b-314">프로젝트 루트에 새 *src* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-314">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="da80b-315">그 목적은 프로젝트의 클라이언트 쪽 자산을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-315">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="da80b-316">다음 내용을 포함한 *src/index.html*을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-316">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="da80b-317">앞의 HTML은 홈 페이지의 상용구 마크업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-317">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="da80b-318">새 *src/css* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-318">Create a new *src/css* directory.</span></span> <span data-ttu-id="da80b-319">그 목적은 프로젝트의 *.css* 파일을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-319">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="da80b-320">다음 내용을 포함한 *src/css/main.css*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-320">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="da80b-321">앞의 *main.css* 파일은 앱의 스타일을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-321">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="da80b-322">다음 내용을 포함한 *src/tsconfig.json*을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-322">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="da80b-323">앞의 코드는 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 호환 JavaScript를 생성하도록 TypeScript 컴파일러를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-323">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="da80b-324">다음 내용을 포함한 *src/index.ts*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-324">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="da80b-325">이 TypeScript는 DOM 요소 참조를 조회하여 두 가지 이벤트 핸들러를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-325">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="da80b-326">`keyup`: 이 이벤트는 사용자가 `tbMessage`로 식별되는 텍스트 상자에 내용을 입력할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-326">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="da80b-327">`send` 함수는 사용자가 **Enter** 키를 누를 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-327">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="da80b-328">`click`: 이 이벤트는 사용자가 **보내기** 단추를 클릭할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-328">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="da80b-329">`send` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-329">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="da80b-330">ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="da80b-330">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="da80b-331">`Startup.Configure` 메서드에 제공된 코드는 *Hello World!* 를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-331">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="da80b-332">`app.Run` 메서드를 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 및 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)에 대한 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-332">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="da80b-333">이 코드는 사용자가 전체 URL을 입력하는지 아니면 웹앱의 루트 URL만 입력하는지 관계없이 서버가 *index.html* 파일을 찾아서 제공할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-333">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="da80b-334">`Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-334">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="da80b-335">이는 SignalR 서비스를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-335">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="da80b-336">*/hub* 경로를 `ChatHub` 허브에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-336">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="da80b-337">`Startup.Configure` 메서드의 끝에 다음 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-337">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

::: moniker-end

1. <span data-ttu-id="da80b-338">프로젝트 루트에 *Hubs*라는 새 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-338">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="da80b-339">그 목적은 다음 단계에서 생성되는 SignalR 허브를 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-339">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="da80b-340">다음 내용을 포함한 허브 *Hubs/ChatHub.cs*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-340">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="da80b-341">`ChatHub` 참조를 확인하기 위해 *Startup.cs* 파일의 맨 위에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-341">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="da80b-342">클라이언트 및 서버 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="da80b-342">Enable client and server communication</span></span>

<span data-ttu-id="da80b-343">앱은 현재 메시지를 보낼 간단한 양식을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-343">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="da80b-344">그런 시도를 할 때 아무 일도 일어나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-344">Nothing happens when you try to do so.</span></span> <span data-ttu-id="da80b-345">서버는 특정 경로를 수신 대기하지만 보낸 메시지를 사용하여 아무 일도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-345">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="da80b-346">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-346">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="da80b-347">앞의 명령은 클라이언트가 서버에 메시지를 보낼 수 있도록 하는 [SignalR TypeScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-347">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="da80b-348">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-348">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="da80b-349">앞의 코드는 서버에서 오는 메시지의 수신을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-349">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="da80b-350">`HubConnectionBuilder` 클래스는 서버 연결을 구성하기 위한 새 빌더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-350">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="da80b-351">`withUrl` 함수는 허브 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-351">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="da80b-352">SignalR은 클라이언트 및 서버 간 메시지 교환을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-352">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="da80b-353">각 메시지는 특정 이름을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-353">Each message has a specific name.</span></span> <span data-ttu-id="da80b-354">예를 들어 메시지 영역에 새 메시지를 표시하는 로직을 실행하는 `messageReceived`라는 이름을 가진 메시지가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-354">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="da80b-355">특정 메시지 수신 대기는 `on` 함수를 통해 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-355">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="da80b-356">임의 개수의 메시지 이름을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-356">You can listen to any number of message names.</span></span> <span data-ttu-id="da80b-357">또한 수신 메시지의 작성자 이름 및 내용 등을 메시지에 파라미터로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-357">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="da80b-358">클라이언트가 메시지를 수신한 후 `innerHTML` 특성의 작성자 이름 및 메시지 내용을 사용하여 새 `div` 요소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-358">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="da80b-359">이 요소가 주 `div` 요소에 추가되어 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-359">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="da80b-360">이제 클라이언트가 메시지를 수신할 수 있으므로, 메시지를 보내도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-360">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="da80b-361">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-361">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="da80b-362">WebSockets 연결을 통해 메시지를 보내면 `send` 메서드를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-362">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="da80b-363">이 메서드의 첫 번째 매개 변수는 메시지의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-363">The method's first parameter is the message name.</span></span> <span data-ttu-id="da80b-364">메시지의 데이터는 다른 매개 변수를 통해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-364">The message data inhabits the other parameters.</span></span> <span data-ttu-id="da80b-365">이 예제에서는 `newMessage`로 식별되는 메시지가 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-365">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="da80b-366">메시지는 사용자 이름 및 텍스트 상자에 입력된 사용자 입력으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-366">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="da80b-367">보내기가 작동하면 텍스트 상자 값이 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-367">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="da80b-368">강조 표시된 메서드를 `ChatHub` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-368">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="da80b-369">앞의 코드는 서버가 메시지를 수신한 후 수신된 메시지를 모든 연결된 사용자에게 브로드캐스트합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-369">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="da80b-370">모든 메시지를 수신하기 위해 일반 `on` 메서드를 포함할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-370">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="da80b-371">메시지 이름 뒤에 명명한 메서드로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-371">A method named after the message name suffices.</span></span>

    <span data-ttu-id="da80b-372">이 예에서 TypeScript 클라이언트는 `newMessage`로 식별된 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-372">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="da80b-373">C# `NewMessage` 메서드에는 클라이언트가 보낸 데이터가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-373">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="da80b-374">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)에 대한 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-374">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="da80b-375">수신된 메시지를 허브에 연결된 모든 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-375">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="da80b-376">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="da80b-376">Test the app</span></span>

<span data-ttu-id="da80b-377">다음 단계를 사용하여 앱이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-377">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da80b-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da80b-378">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="da80b-379">WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-379">Run Webpack in *release* mode.</span></span> <span data-ttu-id="da80b-380">**패키지 관리자 콘솔** 창을 사용하여 프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-380">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="da80b-381">프로젝트 루트가 아닌 경우 명령을 입력하기 전에 `cd SignalRWebPack`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-381">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="da80b-382">**디버그** > **디버그하지 않고 시작**을 선택하여 디버거를 연결하지 않고 브라우저에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-382">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="da80b-383">*wwwroot/index.html* 파일은 `http://localhost:<port_number>`에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-383">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="da80b-384">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-384">Open another browser instance (any browser).</span></span> <span data-ttu-id="da80b-385">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-385">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="da80b-386">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-386">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="da80b-387">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-387">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="da80b-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da80b-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="da80b-389">프로젝트 루트에서 다음 명령을 실행하여 WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-389">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="da80b-390">프로젝트 루트에서 다음 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-390">Build and run the app by executing the following command in the project root:</span></span>

    ```console
    dotnet run
    ```

    <span data-ttu-id="da80b-391">웹 서버가 앱을 시작하고 로컬 호스트에서 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-391">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="da80b-392">`http://localhost:<port_number>`에 대해 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-392">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="da80b-393">그러면 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-393">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="da80b-394">주소 표시줄에서 URL을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-394">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="da80b-395">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-395">Open another browser instance (any browser).</span></span> <span data-ttu-id="da80b-396">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-396">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="da80b-397">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-397">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="da80b-398">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="da80b-398">The unique user name and message are displayed on both pages instantly.</span></span>

---

![두 브라우저 창 모두에 표시된 메시지](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="da80b-400">추가 자료</span><span class="sxs-lookup"><span data-stu-id="da80b-400">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
