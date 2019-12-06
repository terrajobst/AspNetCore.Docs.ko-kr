---
title: ASP.NET Core에서 Grunt 사용
author: rick-anderson
description: ASP.NET Core에서 Grunt 사용
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879788"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="8712d-103">ASP.NET Core에서 Grunt 사용</span><span class="sxs-lookup"><span data-stu-id="8712d-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="8712d-104">Grunt는 스크립트 축소, TypeScript 컴파일, 코드 품질 "보풀이 없는" 도구, CSS 이전 프로세서 및 클라이언트 개발을 지원 하기 위해 수행 해야 하는 반복적인 chore을 자동화 하는 JavaScript 작업 runner입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="8712d-105">Grunt는 Visual Studio에서 완벽 하 게 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="8712d-106">이 예제에서는 빈 ASP.NET Core 프로젝트를 시작 지점으로 사용 하 여 클라이언트 빌드 프로세스를 처음부터 자동화 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="8712d-107">완성 된 예제에서는 대상 배포 디렉터리를 정리 하 고, JavaScript 파일을 결합 하 고, 코드 품질을 검사 하 고, JavaScript 파일 콘텐츠를 압축 하 고, 웹 응용 프로그램의 루트에 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="8712d-108">다음 패키지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-108">We will use the following packages:</span></span>

* <span data-ttu-id="8712d-109">**grunt**: grunt task runner 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="8712d-110">**grunt-clean**: 파일이 나 디렉터리를 제거 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="8712d-111">**grunt-jshint**: JavaScript 코드 품질을 검토 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="8712d-112">**grunt-concat**: 파일을 단일 파일로 조인 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="8712d-113">**grunt-uglify**: 크기를 줄이기 위해 JavaScript를 축소 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="8712d-114">**grunt-watch**: 파일 작업을 감시 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="8712d-115">응용 프로그램 준비</span><span class="sxs-lookup"><span data-stu-id="8712d-115">Preparing the application</span></span>

<span data-ttu-id="8712d-116">시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예제 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="8712d-117">TypeScript 파일은 기본 Visual Studio 설정을 사용 하 여 JavaScript로 자동으로 컴파일되며 Grunt를 사용 하 여 처리 하는 원시 자료가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="8712d-118">Visual Studio에서 새 `ASP.NET Web Application`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="8712d-119">**새 ASP.NET 프로젝트** 대화 상자에서 **빈** 템플릿 ASP.NET Core 선택 하 고 확인 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="8712d-120">솔루션 탐색기에서 프로젝트 구조를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="8712d-121">`\src` 폴더는 빈 `wwwroot` 및 `Dependencies` 노드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="8712d-123">프로젝트 디렉터리에 `TypeScript` 라는 새 폴더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="8712d-124">파일을 추가 하기 전에 Visual Studio에 TypeScript 파일에 대해 ' 저장 시 컴파일 ' 옵션이 선택 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="8712d-125">**도구** > **옵션** > **Typescript** > **프로젝트** > **텍스트 편집기** 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript 파일의 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="8712d-127">`TypeScript` 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **> 새 항목 추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="8712d-128">**JavaScript 파일** 항목을 선택 하 고 파일 이름을 *취향* 로 \*.</span><span class="sxs-lookup"><span data-stu-id="8712d-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="8712d-129">아래 TypeScript 코드 줄을 파일에 복사 합니다. 저장할 때 새 *취향* 파일이 JavaScript 원본과 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="8712d-130">**TypeScript** 디렉터리에 두 번째 파일을 추가 하 고 이름을 `Food.ts`로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="8712d-131">아래 코드를 파일에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="8712d-132">NPM 구성</span><span class="sxs-lookup"><span data-stu-id="8712d-132">Configuring NPM</span></span>

<span data-ttu-id="8712d-133">다음으로 NPM를 구성 하 여 grunt 및 grunt를 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="8712d-134">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **> 새 항목 추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="8712d-135">**NPM 구성 파일** 항목을 선택 하 고 기본 이름인 *package*를 그대로 두고 **추가** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="8712d-136">*Package. json* 파일의 `devDependencies` 개체 중괄호 내에 "grunt"를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="8712d-137">Intellisense 목록에서 `grunt`을 선택 하 고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="8712d-138">Visual Studio는 grunt 패키지 이름을 인용 하 고 콜론을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="8712d-139">콜론의 오른쪽에 있는 최신 버전의 패키지를 Intellisense 목록 위쪽에서 선택 합니다. Intellisense가 나타나지 않으면 `Ctrl-Space`를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="8712d-141">NPM은 [의미 체계 버전 관리](https://semver.org/) 를 사용 하 여 종속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="8712d-142">SemVer 라고도 하는 의미 체계 버전 관리는 번호 매기기 체계가 \<주 > 패키지를 식별 합니다.\<부 >입니다. 패치 >를\<합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="8712d-143">Intellisense는 몇 가지 일반적인 선택 항목만 표시 하 여 의미 체계 버전 관리를 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="8712d-144">Intellisense 목록의 맨 위 항목 (위 예제의 0.4.5)은 최신 안정적인 패키지 버전으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="8712d-145">캐럿 (^) 기호는 가장 최근의 주 버전과 일치 하 고 물결표 (~)는 가장 최근의 부 버전과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="8712d-146">SemVer에서 제공 하는 전체 표현을에 대 한 지침으로 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8712d-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="8712d-147">아래 예제와 같이 *clean*, *jshint*, *concat*, *uglify*및 *watch* 에 대 한\* grunt 패키지를 로드 하는 데 더 많은 종속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="8712d-148">버전은 예제와 일치 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="8712d-149">*Package. json* 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-149">Save the *package.json* file.</span></span>

<span data-ttu-id="8712d-150">각 `devDependencies` 항목에 대 한 패키지가 각 패키지에 필요한 파일과 함께 다운로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="8712d-151">**솔루션 탐색기**의 **모든 파일 표시** 단추를 사용 하도록 설정 하면 *node_modules* 디렉터리에서 패키지 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="8712d-153">필요한 경우 `Dependencies\NPM`을 마우스 오른쪽 단추로 클릭 하 고 **패키지 복원** 메뉴 옵션을 선택 하 여 **솔루션 탐색기** 에서 종속성을 수동으로 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="8712d-155">Grunt 구성</span><span class="sxs-lookup"><span data-stu-id="8712d-155">Configuring Grunt</span></span>

<span data-ttu-id="8712d-156">Grunt는 수동으로 실행 하거나 Visual Studio의 이벤트에 따라 자동으로 실행 되도록 구성할 수 있는 작업을 정의, 로드 및 등록 하는 *Gruntfile* 라는 매니페스트를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="8712d-157">프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **추가** > **새 항목**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="8712d-158">**JavaScript 파일** 항목 템플릿을 선택 하 고 이름을 *Gruntfile*로 변경한 다음 **추가** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="8712d-159">*Gruntfile*에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="8712d-160">`initConfig` 함수는 각 패키지에 대 한 옵션을 설정 하 고 나머지 모듈은 작업을 로드 및 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="8712d-161">`initConfig` 함수 내에서 아래 예제 *Gruntfile* 같이 `clean` 작업에 대 한 옵션을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="8712d-162">`clean` 태스크는 디렉터리 문자열의 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="8712d-163">이 작업은 *wwwroot/lib* 에서 파일을 제거 하 고 전체 */임시* 디렉터리를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="8712d-164">`initConfig` 함수 아래에 `grunt.loadNpmTasks`에 대 한 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="8712d-165">그러면 Visual Studio에서 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="8712d-166">*Gruntfile*를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="8712d-167">파일은 아래 스크린샷에서와 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-167">The file should look something like the screenshot below.</span></span>

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="8712d-169">*Gruntfile* 를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **작업 Runner 탐색기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="8712d-170">**작업 러너 탐색기** 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-170">The **Task Runner Explorer** window will open.</span></span>

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="8712d-172">**작업 러너 탐색기**의 **작업** 아래에 `clean` 표시 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![작업 러너 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="8712d-174">정리 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **실행** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="8712d-175">명령 창에 작업 진행률이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-175">A command window displays progress of the task.</span></span>

    ![작업 러너 탐색기 정리 작업 실행](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="8712d-177">아직 정리할 파일이 나 디렉터리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="8712d-178">원하는 경우 솔루션 탐색기에서 수동으로 만든 다음 테스트로 정리 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="8712d-179">`initConfig` 함수에서 아래 코드를 사용 하 여 `concat`에 대 한 항목을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="8712d-180">`src` 속성 배열에는 결합할 파일이 결합 되는 순서 대로 나열 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="8712d-181">`dest` 속성은 생성 된 결합 된 파일에 경로를 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="8712d-182">위의 코드에서 `all` 속성은 대상의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="8712d-183">대상은 여러 빌드 환경을 허용 하기 위해 일부 Grunt 작업에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="8712d-184">IntelliSense를 사용 하 여 기본 제공 대상을 보거나 직접 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="8712d-185">아래 코드를 사용 하 여 `jshint` 작업을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="8712d-186">Jshint `code-quality` 유틸리티는 *temp* 디렉터리에 있는 모든 JavaScript 파일에 대해 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="8712d-187">"-W069" 옵션은 JavaScript에서 대괄호 구문을 사용 하 여 점 표기법 대신 (`Tastes["Sweet"]` `Tastes.Sweet`대신에 속성을 할당할 때 발생 하는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="8712d-188">옵션은 나머지 프로세스를 계속할 수 있도록 경고를 끕니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="8712d-189">아래 코드를 사용 하 여 `uglify` 작업을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="8712d-190">이 작업은 축소 *파일* 이름\>. s t a t e m. s e r v e r *\<파일 이름*에 대 한 표준 명명 규칙에 따라 wwwroot/lib에 결과 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="8712d-191">`grunt-contrib-clean`를 로드 하는 `grunt.loadNpmTasks`에 대 한 호출에서 아래 코드를 사용 하 여 jshint, concat 및 uglify에 대 한 동일한 호출을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="8712d-192">*Gruntfile*를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="8712d-193">파일은 아래 예제와 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-193">The file should look something like the example below.</span></span>

    ![complete grunt 파일 예제](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="8712d-195">**작업 러너 탐색기** 작업 목록에 `clean`, `concat`, `jshint` 및 `uglify` 태스크가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="8712d-196">각 작업을 순서 대로 실행 하 고 **솔루션 탐색기**의 결과를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="8712d-197">각 태스크는 오류 없이 실행 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-197">Each task should run without errors.</span></span>

    ![작업 러너 탐색기 각 작업 실행](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="8712d-199">Concat 작업은 결합 된 새 *.js* 파일을 만들고 임시 디렉터리에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="8712d-200">`jshint` 작업은 단순히 실행 되 고 출력을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="8712d-201">`uglify` 태스크는 결합 된 새 *.70.js* 파일을 만들어 *wwwroot/lib*에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="8712d-202">완료 되 면 솔루션은 아래 스크린샷에서와 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![모든 작업 후의 솔루션 탐색기](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="8712d-204">각 패키지에 대 한 옵션에 대 한 자세한 내용을 보려면 [https://www.npmjs.com/](https://www.npmjs.com/) 를 방문 하 여 기본 페이지의 검색 상자에서 패키지 이름을 조회 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="8712d-205">예를 들어 grunt 패키지를 조회 하 여 모든 매개 변수를 설명 하는 설명서 링크를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="8712d-206">요약정리</span><span class="sxs-lookup"><span data-stu-id="8712d-206">All together now</span></span>

<span data-ttu-id="8712d-207">Grunt `registerTask()` 메서드를 사용 하 여 특정 순서로 일련의 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="8712d-208">예를 들어 위 예제 단계를 정리 > concat-> jshint-> uglify 순서에서 실행 하려면 모듈에 아래 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="8712d-209">InitConfig 외부의 loadNpmTasks () 호출과 같은 수준에 코드를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="8712d-210">새 작업은 작업 러너 탐색기의 별칭 작업 아래에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="8712d-211">마우스 오른쪽 단추를 클릭 하 고 다른 작업과 마찬가지로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="8712d-212">`all` 태스크는 `clean`, `concat`, `jshint` 및 `uglify`순서 대로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="8712d-214">변경 내용 감시하기</span><span class="sxs-lookup"><span data-stu-id="8712d-214">Watching for changes</span></span>

<span data-ttu-id="8712d-215">`watch` 태스크는 파일 및 디렉터리에 대 한 눈을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="8712d-216">변경 내용이 검색 되 면 조사식이 자동으로 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="8712d-217">InitConfig에 아래 코드를 추가 하 여 TypeScript 디렉터리에 \*.js 파일의 변경 내용을 감시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="8712d-218">JavaScript 파일이 변경 되 면 `watch`에서 `all` 작업을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="8712d-219">`loadNpmTasks()`에 대 한 호출을 추가 하 여 Task Runner 탐색기에서 `watch` 작업을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="8712d-220">작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="8712d-221">실행 중인 조사식 작업을 보여 주는 명령 창에 "대기 중 ..."이 표시 됩니다. 메시지.</span><span class="sxs-lookup"><span data-stu-id="8712d-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="8712d-222">TypeScript 파일 중 하나를 열고, 공백을 추가한 다음, 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="8712d-223">그러면 조사식 태스크가 트리거되고 다른 작업이 순서 대로 실행 되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="8712d-224">아래 스크린샷은 샘플 실행을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-224">The screenshot below shows a sample run.</span></span>

![실행 중인 작업 출력](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="8712d-226">Visual Studio 이벤트에 바인딩</span><span class="sxs-lookup"><span data-stu-id="8712d-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="8712d-227">Visual Studio에서 작업할 때마다 수동으로 작업을 시작 하려는 경우가 아니면 빌드 **전**, **빌드 후** **정리**및 **프로젝트 열기** 이벤트 이후에 작업을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="8712d-228">Visual Studio가 열릴 때마다 실행 되도록 `watch`를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="8712d-229">작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **바인딩** > **프로젝트 열기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![열려 있는 프로젝트에 작업 바인딩](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="8712d-231">프로젝트를 언로드하고 다시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-231">Unload and reload the project.</span></span> <span data-ttu-id="8712d-232">프로젝트가 다시 로드 되 면 조사식 태스크가 자동으로 실행 되기 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="8712d-233">요약</span><span class="sxs-lookup"><span data-stu-id="8712d-233">Summary</span></span>

<span data-ttu-id="8712d-234">Grunt는 대부분의 클라이언트 빌드 작업을 자동화 하는 데 사용할 수 있는 강력한 작업 러너입니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="8712d-235">Grunt는 NPM를 활용 하 여 패키지를 제공 하 고 기능 도구를 Visual Studio와 통합 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="8712d-236">Visual Studio의 작업 러너 탐색기는 구성 파일에 대 한 변경 내용을 검색 하 고 작업을 실행 하 고, 실행 중인 작업을 보고, 작업을 Visual Studio 이벤트에 바인딩하는 편리한 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8712d-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
