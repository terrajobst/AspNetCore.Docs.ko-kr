---
title: ASP.NET Core에서 사용 하 여 Grunt
author: rick-anderson
description: ASP.NET Core에서 사용 하 여 Grunt
ms.author: riande
ms.date: 05/14/2019
uid: client-side/using-grunt
ms.openlocfilehash: 4d9b6cf6f9a0007e9722bc054f0d9a7608f1473b
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620994"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="9a915-103">ASP.NET Core에서 사용 하 여 Grunt</span><span class="sxs-lookup"><span data-stu-id="9a915-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="9a915-104">[Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="9a915-104">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="9a915-105">Grunt은 스크립트 축소, TypeScript 컴파일, 코드 품질 다름이 도구, 프로세서를 전 하는 CSS, 및 클라이언트 개발을 지원 하기 위해 수행 해야 하는 모든 반복 작업에 대 한 자동화 하는 JavaScript 작업 실행 기는 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-105">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="9a915-106">Visual Studio에서 grunt 완전히 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-106">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="9a915-107">이 예제에서는 빈 ASP.NET Core 프로젝트의 시작 점으로에서 클라이언트 빌드 프로세스를 자동화 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-107">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="9a915-108">완성 된 예제에서는 대상 배포 디렉터리를 정리, JavaScript 파일을 결합, 코드 품질을 확인, JavaScript 파일 콘텐츠를 압축 및 웹 응용 프로그램의 루트에 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-108">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="9a915-109">다음 패키지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-109">We will use the following packages:</span></span>

* <span data-ttu-id="9a915-110">**grunt**: Grunt 작업 실행 기 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-110">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="9a915-111">**grunt-contrib-clean**: 파일 또는 디렉터리를 제거 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-111">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="9a915-112">**grunt-contrib-jshint**: JavaScript 코드 품질을 검토 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-112">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="9a915-113">**grunt-contrib-concat**: 단일 파일에 파일을 조인 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-113">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="9a915-114">**grunt-contrib-uglify**: 크기를 줄이기 위해 JavaScript를 축소 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-114">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="9a915-115">**grunt-contrib-watch**: 파일 작업을 감시 하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-115">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="9a915-116">응용 프로그램 준비</span><span class="sxs-lookup"><span data-stu-id="9a915-116">Preparing the application</span></span>

<span data-ttu-id="9a915-117">시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예제 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-117">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="9a915-118">TypeScript 파일 자동으로 기본 Visual Studio 설정을 사용 하 여 JavaScript로 컴파일되는 및 Grunt를 사용 하 여 처리 하는 데는 원료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-118">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="9a915-119">Visual Studio에서 만드는 새 `ASP.NET Web Application`합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-119">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="9a915-120">에 **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET Core를 선택 **빈** 템플릿을 확인 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-120">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="9a915-121">솔루션 탐색기에서 프로젝트 구조를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-121">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="9a915-122">합니다 `\src` 폴더에 빈 포함 `wwwroot` 및 `Dependencies` 노드.</span><span class="sxs-lookup"><span data-stu-id="9a915-122">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="9a915-124">라는 새 폴더 추가 `TypeScript` 프로젝트 디렉터리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-124">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="9a915-125">모든 파일을 추가 하기 전에 Visual Studio 옵션에 있는지 확인 ' 컴파일 저장 시 ' 체크 TypeScript 파일에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-125">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="9a915-126">이동할 **도구가** > **옵션** > **텍스트 편집기** > **Typescript**  >  **프로젝트**:</span><span class="sxs-lookup"><span data-stu-id="9a915-126">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript 파일 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="9a915-128">마우스 오른쪽 단추로 클릭 합니다 `TypeScript` 디렉터리 및 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-128">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9a915-129">선택 된 **JavaScript 파일** 항목 및 파일 이름을 *Tastes.ts* (참고는 \*.ts 확장).</span><span class="sxs-lookup"><span data-stu-id="9a915-129">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="9a915-130">아래 TypeScript 코드 줄을 파일에 복사 (저장할 때, 새 *Tastes.js* JavaScript 소스 파일이 표시 됩니다).</span><span class="sxs-lookup"><span data-stu-id="9a915-130">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="9a915-131">두 번째 파일을 추가 합니다 **TypeScript** 디렉터리 이름을 `Food.ts`입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-131">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="9a915-132">파일에 아래 코드를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-132">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="9a915-133">NPM 구성</span><span class="sxs-lookup"><span data-stu-id="9a915-133">Configuring NPM</span></span>

<span data-ttu-id="9a915-134">다음으로, grunt 및 grunt 작업을 다운로드 하려면 NPM을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-134">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="9a915-135">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-135">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9a915-136">선택는 **NPM 구성 파일** 항목, 기본 이름을 그대로 *package.json*를 클릭 합니다 **추가** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-136">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="9a915-137">에 *package.json* 파일에 `devDependencies` 중괄호 개체 "grunt"를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-137">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="9a915-138">선택 `grunt` Intellisense에서 나열 하 고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-138">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="9a915-139">Visual Studio grunt 패키지 이름을 따옴표를 콜론을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-139">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="9a915-140">콜론의 오른쪽에 Intellisense 목록 맨 위에서 패키지의 안정적인 최신 버전 선택 (키를 눌러 `Ctrl-Space` Intellisense 표시 하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="9a915-140">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="9a915-142">NPM을 사용 [유의 적 버전](http://semver.org/) 종속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-142">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="9a915-143">번호 매기기 체계를 사용 하 여 패키지를 식별 하는 의미 체계 버전 관리, 라고도 SemVer \<주요 >.\< 부 버전 >. \<패치 >.</span><span class="sxs-lookup"><span data-stu-id="9a915-143">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="9a915-144">Intellisense만 몇 가지 일반적인 옵션을 표시 하 여 의미 체계 버전 관리를 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-144">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="9a915-145">(위의 예에서 0.4.5) Intellisense 목록에 맨 위 항목에는 패키지의 안정적인 최신 버전으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-145">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="9a915-146">캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 항목과 물결표 (~) 최신 부 버전.</span><span class="sxs-lookup"><span data-stu-id="9a915-146">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="9a915-147">참조 된 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) SemVer 제공 하는 전체 표현 가이드로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-147">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="9a915-148">로드에 대 한 자세한 종속성 grunt 추가-contrib-\* 에 대 한 패키지 *정리*를 *jshint*를 *concat*를 *uglify*, 및 *조사식* 아래 예와에서 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-148">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="9a915-149">버전은 예제와 일치 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-149">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="9a915-150">저장 된 *package.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-150">Save the *package.json* file.</span></span>

<span data-ttu-id="9a915-151">각 패키지 `devDependencies` 항목 각 패키지에 필요한 파일과 함께 다운로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-151">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="9a915-152">패키지 파일을 찾을 수 있습니다 합니다 *node_modules* 사용 하 여 디렉터리를 **모든 파일 표시** 단추 **솔루션 탐색기**합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-152">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="9a915-154">해야 할 경우 종속성을 수동으로 복원할 수 있습니다 **솔루션 탐색기** 마우스 오른쪽 단추로 클릭 하 여 `Dependencies\NPM` 선택 하는 **패키지 복원** 메뉴 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-154">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="9a915-156">Grunt 구성</span><span class="sxs-lookup"><span data-stu-id="9a915-156">Configuring Grunt</span></span>

<span data-ttu-id="9a915-157">Grunt 이라는 매니페스트를 사용 하 여 구성 됩니다 *Gruntfile.js* 정의 하는, 로드 하 고 수동으로 실행 하거나 Visual Studio에서 이벤트에 따라 자동으로 실행 되도록 구성할 수 있는 작업을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-157">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="9a915-158">프로젝트를 마우스 오른쪽 단추로 누르고 **추가** > **새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-158">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="9a915-159">선택 합니다 **JavaScript 파일** 항목 템플릿, 이름을 *Gruntfile.js*, 클릭 하 고는 **추가** 단추.</span><span class="sxs-lookup"><span data-stu-id="9a915-159">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="9a915-160">다음 코드를 추가 *Gruntfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-160">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="9a915-161">`initConfig` 각 패키지에 대 한 옵션을 설정 하는 함수 및 모듈의 나머지 부분 로드 하 고 작업을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-161">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="9a915-162">내 합니다 `initConfig` 함수에 대 한 옵션을 추가 합니다 합니다 `clean` 예제에 표시 된 대로 작업 *Gruntfile.js* 아래.</span><span class="sxs-lookup"><span data-stu-id="9a915-162">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="9a915-163">`clean` 작업 디렉터리 문자열 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-163">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="9a915-164">이 태스크에서 파일을 제거 *wwwroot/lib* 전체를 제거 하 고 *임시/* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-164">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="9a915-165">아래는 `initConfig` 함수를 호출을 추가 `grunt.loadNpmTasks`합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-165">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="9a915-166">이렇게 하면 작업 실행 가능한 Visual Studio에서.</span><span class="sxs-lookup"><span data-stu-id="9a915-166">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="9a915-167">저장할 *Gruntfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-167">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9a915-168">파일은 아래 스크린샷과 같은 비슷해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-168">The file should look something like the screenshot below.</span></span>

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="9a915-170">마우스 오른쪽 단추로 클릭 *Gruntfile.js* 선택한 **Task Runner 탐색기** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-170">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="9a915-171">합니다 **Task Runner 탐색기** 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-171">The **Task Runner Explorer** window will open.</span></span>

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="9a915-173">확인 `clean` 아래에 표시 **태스크** 에 **Task Runner 탐색기**합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-173">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![작업 러너 탐색기 태스크 목록](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="9a915-175">정리 작업을 마우스 오른쪽 단추로 클릭 **실행** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-175">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="9a915-176">명령 창에는 작업의 진행률이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-176">A command window displays progress of the task.</span></span>

    ![작업 러너 탐색기 실행 정리 작업](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="9a915-178">파일 또는 디렉터리가 아직 정리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-178">There are no files or directories to clean yet.</span></span> <span data-ttu-id="9a915-179">원하는 경우 솔루션 탐색기에서이 수동으로 만들 수 있으며 다음 테스트로 정리 작업을 실행 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-179">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="9a915-180">에 `initConfig` 함수에 대 한 항목을 추가 `concat` 아래 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-180">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="9a915-181">`src` 속성 배열 이러한 결합 해야 순서로 결합 하는 파일을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-181">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="9a915-182">`dest` 속성 생성 되는 결합된 된 파일에 경로 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-182">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9a915-183">`all` 위의 코드에서 속성은 대상의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-183">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="9a915-184">목표는 여러 빌드 환경 수 있도록 일부 Grunt 작업에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-184">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="9a915-185">IntelliSense를 사용 하 여 기본 제공 대상을 보거나 직접 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-185">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="9a915-186">추가 된 `jshint` 아래 코드를 사용 하 여 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-186">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="9a915-187">jshint `code-quality` 있는 모든 JavaScript 파일에 대 한 유틸리티를 실행 합니다 *temp* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-187">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9a915-188">옵션 "-W069"는 오류가 때 생성 한 jshint JavaScript는 괄호, 즉 점 표기법 대신 속성을 할당 하는 구문을 `Tastes["Sweet"]` 대신 `Tastes.Sweet`합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-188">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="9a915-189">옵션을 계속 하는 프로세스의 나머지 부분을 허용 하도록 경고를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-189">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="9a915-190">추가 된 `uglify` 아래 코드를 사용 하 여 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-190">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="9a915-191">축소 작업은 *combined.js* 파일이 임시 디렉터리에서 찾은 wwwroot/lib 표준 명명 규칙에 따라에 결과 파일을 만듭니다  *\<파일 이름\>. min.js*.</span><span class="sxs-lookup"><span data-stu-id="9a915-191">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="9a915-192">에 대 한 호출 아래의 `grunt.loadNpmTasks` 로드 하는 `grunt-contrib-clean`에서 jshint, concat에 대 한 동일한 호출을 포함 하 고 아래 코드를 사용 하 여 uglify입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-192">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="9a915-193">저장할 *Gruntfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-193">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9a915-194">파일은 아래 예제와 비슷해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-194">The file should look something like the example below.</span></span>

    ![전체 grunt 파일 예제](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="9a915-196">있음을 합니다 **Task Runner 탐색기** 작업 목록은 `clean`, `concat`, `jshint` 및 `uglify` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-196">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="9a915-197">순서로 각 태스크를 실행 하 고 결과를 관찰 **솔루션 탐색기**합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-197">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="9a915-198">각 태스크는 오류 없이 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-198">Each task should run without errors.</span></span>

    ![각 태스크를 실행 하는 task runner 탐색기](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="9a915-200">Concat 태스크를 만듭니다 *combined.js* 파일을 임시 디렉터리에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-200">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="9a915-201">`jshint` 단순히 작업 실행 및 출력을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-201">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="9a915-202">합니다 `uglify` 태스크를 만듭니다 *combined.min.js* 파일과에 배치 *wwwroot/lib*합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-202">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="9a915-203">완료 되 면 솔루션 아래 스크린샷과 비슷하게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-203">On completion, the solution should look something like the screenshot below:</span></span>

    ![솔루션 탐색기에서 모든 작업](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="9a915-205">각 패키지에 대 한 옵션에 대 한 자세한 내용은 방문 [ https://www.npmjs.com/ ](https://www.npmjs.com/) 및 기본 페이지에서 검색 상자에 패키지 이름 조회 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-205">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="9a915-206">예를 들어, 모든 매개 변수를 설명 하는 설명서 링크를 표시 하려면 grunt contrib 정리 패키지를 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-206">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="9a915-207">모두 통합</span><span class="sxs-lookup"><span data-stu-id="9a915-207">All together now</span></span>

<span data-ttu-id="9a915-208">Grunt를 사용 하 여 `registerTask()` 특정 순서로 일련의 작업을 실행 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-208">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="9a915-209">예를 들어, 위의 단계를 정리 하는 순서로-> 예제를 실행 하려면 concat-> jshint-> uglify 모듈에 아래 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-209">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="9a915-210">코드는 initConfig 외부 loadNpmTasks() 호출에 동일한 수준에 추가 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-210">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="9a915-211">별칭 작업에서 Task Runner 탐색기에서 새 작업이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-211">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="9a915-212">마우스 오른쪽 단추로 클릭 하 고 다른 작업 처럼 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-212">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="9a915-213">`all` 태스크가 실행 됩니다 `clean`, `concat`, `jshint` 및 `uglify`를 순서 대로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-213">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="9a915-215">변경 내용 감시하기</span><span class="sxs-lookup"><span data-stu-id="9a915-215">Watching for changes</span></span>

<span data-ttu-id="9a915-216">`watch` 작업 파일 및 디렉터리를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-216">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="9a915-217">시계 변경 내용을 감지한 경우 자동으로 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-217">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="9a915-218">아래 코드에 변경 내용을 감시 하려면 initConfig 추가할 \*TypeScript 디렉터리의.js 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-218">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="9a915-219">JavaScript 파일이 변경 되 면 `watch` 실행될지는 `all` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-219">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="9a915-220">호출을 추가 `loadNpmTasks()` 표시할는 `watch` Task Runner 탐색기에서 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-220">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="9a915-221">Task Runner 탐색기에 있는 watch 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-221">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="9a915-222">"대기 중..."이 실행 중인 보기 작업을 보여 주는 명령 창에 표시 됩니다. 메시지.</span><span class="sxs-lookup"><span data-stu-id="9a915-222">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="9a915-223">TypeScript 파일 중 하나를 열고 하 고 공백을 추가 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-223">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="9a915-224">조사식 작업 트리거 되 고 순서 대로 실행 하는 다른 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-224">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="9a915-225">다음 스크린샷은 샘플 실행을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-225">The screenshot below shows a sample run.</span></span>

![작업 출력을 실행](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="9a915-227">Visual Studio 이벤트에 바인딩</span><span class="sxs-lookup"><span data-stu-id="9a915-227">Binding to Visual Studio events</span></span>

<span data-ttu-id="9a915-228">Visual Studio에서 작업할 때마다 작업을 수동으로 시작 하려는 경우가 아니면 작업을 바인딩할 수 있습니다 **하기 전에 빌드**, **후 빌드**합니다 **정리**, 및  **프로젝트 열기** 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-228">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="9a915-229">바인딩 하겠습니다 `watch` Visual Studio가 열릴 때마다 실행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-229">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="9a915-230">Task Runner 탐색기에서 보기 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **바인딩 > 프로젝트 열기** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-230">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![프로젝트 열기 작업을 바인딩합니다](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="9a915-232">언로드하고 프로젝트를 다시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-232">Unload and reload the project.</span></span> <span data-ttu-id="9a915-233">프로젝트 다시 로드 되 면 조사식 작업 실행을 자동으로 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-233">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="9a915-234">요약</span><span class="sxs-lookup"><span data-stu-id="9a915-234">Summary</span></span>

<span data-ttu-id="9a915-235">Grunt를 강력한 작업 실행 기 대부분의 클라이언트 빌드 작업을 자동화 하는 경우</span><span class="sxs-lookup"><span data-stu-id="9a915-235">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="9a915-236">Grunt NPM의 패키지 및 Visual Studio와의 통합 도구 기능 제공을 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-236">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="9a915-237">Visual Studio의 Task Runner 탐색기 구성 파일의 변경 사항을 감지 하 고 작업을 실행 하 고 실행 중인 작업을 보고 Visual Studio 이벤트에 작업을 바인딩할 하는 편리한 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a915-237">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
