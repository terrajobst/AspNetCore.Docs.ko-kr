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
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core에서 Grunt 사용

Grunt는 스크립트 축소, TypeScript 컴파일, 코드 품질 "보풀이 없는" 도구, CSS 이전 프로세서 및 클라이언트 개발을 지원 하기 위해 수행 해야 하는 반복적인 chore을 자동화 하는 JavaScript 작업 runner입니다. Grunt는 Visual Studio에서 완벽 하 게 지원 됩니다.

이 예제에서는 빈 ASP.NET Core 프로젝트를 시작 지점으로 사용 하 여 클라이언트 빌드 프로세스를 처음부터 자동화 하는 방법을 보여 줍니다.

완성 된 예제에서는 대상 배포 디렉터리를 정리 하 고, JavaScript 파일을 결합 하 고, 코드 품질을 검사 하 고, JavaScript 파일 콘텐츠를 압축 하 고, 웹 응용 프로그램의 루트에 배포 합니다. 다음 패키지를 사용 합니다.

* **grunt**: grunt task runner 패키지입니다.

* **grunt-clean**: 파일이 나 디렉터리를 제거 하는 플러그 인입니다.

* **grunt-jshint**: JavaScript 코드 품질을 검토 하는 플러그 인입니다.

* **grunt-concat**: 파일을 단일 파일로 조인 하는 플러그 인입니다.

* **grunt-uglify**: 크기를 줄이기 위해 JavaScript를 축소 하는 플러그 인입니다.

* **grunt-watch**: 파일 작업을 감시 하는 플러그 인입니다.

## <a name="preparing-the-application"></a>응용 프로그램 준비

시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예제 파일을 추가 합니다. TypeScript 파일은 기본 Visual Studio 설정을 사용 하 여 JavaScript로 자동으로 컴파일되며 Grunt를 사용 하 여 처리 하는 원시 자료가 됩니다.

1. Visual Studio에서 새 `ASP.NET Web Application`를 만듭니다.

2. **새 ASP.NET 프로젝트** 대화 상자에서 **빈** 템플릿 ASP.NET Core 선택 하 고 확인 단추를 클릭 합니다.

3. 솔루션 탐색기에서 프로젝트 구조를 검토 합니다. `\src` 폴더는 빈 `wwwroot` 및 `Dependencies` 노드를 포함 합니다.

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. 프로젝트 디렉터리에 `TypeScript` 라는 새 폴더를 추가 합니다.

5. 파일을 추가 하기 전에 Visual Studio에 TypeScript 파일에 대해 ' 저장 시 컴파일 ' 옵션이 선택 되어 있는지 확인 합니다. **도구** > **옵션** > **Typescript** > **프로젝트** > **텍스트 편집기** 로 이동 합니다.

    ![TypeScript 파일의 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. `TypeScript` 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **> 새 항목 추가** 를 선택 합니다. **JavaScript 파일** 항목을 선택 하 고 파일 이름을 *취향* 로 \*. 아래 TypeScript 코드 줄을 파일에 복사 합니다. 저장할 때 새 *취향* 파일이 JavaScript 원본과 함께 표시 됩니다.

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. **TypeScript** 디렉터리에 두 번째 파일을 추가 하 고 이름을 `Food.ts`로 만듭니다. 아래 코드를 파일에 복사 합니다.

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

## <a name="configuring-npm"></a>NPM 구성

다음으로 NPM를 구성 하 여 grunt 및 grunt를 다운로드 합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **> 새 항목 추가** 를 선택 합니다. **NPM 구성 파일** 항목을 선택 하 고 기본 이름인 *package*를 그대로 두고 **추가** 단추를 클릭 합니다.

2. *Package. json* 파일의 `devDependencies` 개체 중괄호 내에 "grunt"를 입력 합니다. Intellisense 목록에서 `grunt`을 선택 하 고 Enter 키를 누릅니다. Visual Studio는 grunt 패키지 이름을 인용 하 고 콜론을 추가 합니다. 콜론의 오른쪽에 있는 최신 버전의 패키지를 Intellisense 목록 위쪽에서 선택 합니다. Intellisense가 나타나지 않으면 `Ctrl-Space`를 누릅니다.

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM은 [의미 체계 버전 관리](https://semver.org/) 를 사용 하 여 종속성을 구성 합니다. SemVer 라고도 하는 의미 체계 버전 관리는 번호 매기기 체계가 \<주 > 패키지를 식별 합니다.\<부 >입니다. 패치 >를\<합니다. Intellisense는 몇 가지 일반적인 선택 항목만 표시 하 여 의미 체계 버전 관리를 간소화 합니다. Intellisense 목록의 맨 위 항목 (위 예제의 0.4.5)은 최신 안정적인 패키지 버전으로 간주 됩니다. 캐럿 (^) 기호는 가장 최근의 주 버전과 일치 하 고 물결표 (~)는 가장 최근의 부 버전과 일치 합니다. SemVer에서 제공 하는 전체 표현을에 대 한 지침으로 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) 를 참조 하세요.

3. 아래 예제와 같이 *clean*, *jshint*, *concat*, *uglify*및 *watch* 에 대 한\* grunt 패키지를 로드 하는 데 더 많은 종속성을 추가 합니다. 버전은 예제와 일치 하지 않아도 됩니다.

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

4. *Package. json* 파일을 저장 합니다.

각 `devDependencies` 항목에 대 한 패키지가 각 패키지에 필요한 파일과 함께 다운로드 됩니다. **솔루션 탐색기**의 **모든 파일 표시** 단추를 사용 하도록 설정 하면 *node_modules* 디렉터리에서 패키지 파일을 찾을 수 있습니다.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 필요한 경우 `Dependencies\NPM`을 마우스 오른쪽 단추로 클릭 하 고 **패키지 복원** 메뉴 옵션을 선택 하 여 **솔루션 탐색기** 에서 종속성을 수동으로 복원할 수 있습니다.

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt 구성

Grunt는 수동으로 실행 하거나 Visual Studio의 이벤트에 따라 자동으로 실행 되도록 구성할 수 있는 작업을 정의, 로드 및 등록 하는 *Gruntfile* 라는 매니페스트를 사용 하 여 구성 됩니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **추가** > **새 항목**을 선택 합니다. **JavaScript 파일** 항목 템플릿을 선택 하 고 이름을 *Gruntfile*로 변경한 다음 **추가** 단추를 클릭 합니다.

1. *Gruntfile*에 다음 코드를 추가 합니다. `initConfig` 함수는 각 패키지에 대 한 옵션을 설정 하 고 나머지 모듈은 작업을 로드 및 등록 합니다.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. `initConfig` 함수 내에서 아래 예제 *Gruntfile* 같이 `clean` 작업에 대 한 옵션을 추가 합니다. `clean` 태스크는 디렉터리 문자열의 배열을 허용 합니다. 이 작업은 *wwwroot/lib* 에서 파일을 제거 하 고 전체 */임시* 디렉터리를 제거 합니다.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. `initConfig` 함수 아래에 `grunt.loadNpmTasks`에 대 한 호출을 추가 합니다. 그러면 Visual Studio에서 작업을 실행할 수 있습니다.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile*를 저장 합니다. 파일은 아래 스크린샷에서와 같이 표시 됩니다.

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *Gruntfile* 를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **작업 Runner 탐색기** 를 선택 합니다. **작업 러너 탐색기** 창이 열립니다.

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

1. **작업 러너 탐색기**의 **작업** 아래에 `clean` 표시 되는지 확인 합니다.

    ![작업 러너 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

1. 정리 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **실행** 을 선택 합니다. 명령 창에 작업 진행률이 표시 됩니다.

    ![작업 러너 탐색기 정리 작업 실행](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 아직 정리할 파일이 나 디렉터리가 없습니다. 원하는 경우 솔루션 탐색기에서 수동으로 만든 다음 테스트로 정리 작업을 실행할 수 있습니다.

1. `initConfig` 함수에서 아래 코드를 사용 하 여 `concat`에 대 한 항목을 추가 합니다.

    `src` 속성 배열에는 결합할 파일이 결합 되는 순서 대로 나열 됩니다. `dest` 속성은 생성 된 결합 된 파일에 경로를 할당 합니다.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 위의 코드에서 `all` 속성은 대상의 이름입니다. 대상은 여러 빌드 환경을 허용 하기 위해 일부 Grunt 작업에서 사용 됩니다. IntelliSense를 사용 하 여 기본 제공 대상을 보거나 직접 지정할 수 있습니다.

1. 아래 코드를 사용 하 여 `jshint` 작업을 추가 합니다.

    Jshint `code-quality` 유틸리티는 *temp* 디렉터리에 있는 모든 JavaScript 파일에 대해 실행 됩니다.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > "-W069" 옵션은 JavaScript에서 대괄호 구문을 사용 하 여 점 표기법 대신 (`Tastes["Sweet"]` `Tastes.Sweet`대신에 속성을 할당할 때 발생 하는 오류입니다. 옵션은 나머지 프로세스를 계속할 수 있도록 경고를 끕니다.

1. 아래 코드를 사용 하 여 `uglify` 작업을 추가 합니다.

    이 작업은 축소 *파일* 이름\>. s t a t e m. s e r v e r *\<파일 이름*에 대 한 표준 명명 규칙에 따라 wwwroot/lib에 결과 파일을 만듭니다.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. `grunt-contrib-clean`를 로드 하는 `grunt.loadNpmTasks`에 대 한 호출에서 아래 코드를 사용 하 여 jshint, concat 및 uglify에 대 한 동일한 호출을 포함 합니다.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile*를 저장 합니다. 파일은 아래 예제와 같이 표시 됩니다.

    ![complete grunt 파일 예제](using-grunt/_static/gruntfile-js-complete.png)

1. **작업 러너 탐색기** 작업 목록에 `clean`, `concat`, `jshint` 및 `uglify` 태스크가 포함 되어 있습니다. 각 작업을 순서 대로 실행 하 고 **솔루션 탐색기**의 결과를 확인 합니다. 각 태스크는 오류 없이 실행 되어야 합니다.

    ![작업 러너 탐색기 각 작업 실행](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat 작업은 결합 된 새 *.js* 파일을 만들고 임시 디렉터리에 배치 합니다. `jshint` 작업은 단순히 실행 되 고 출력을 생성 하지 않습니다. `uglify` 태스크는 결합 된 새 *.70.js* 파일을 만들어 *wwwroot/lib*에 배치 합니다. 완료 되 면 솔루션은 아래 스크린샷에서와 같이 표시 됩니다.

    ![모든 작업 후의 솔루션 탐색기](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 각 패키지에 대 한 옵션에 대 한 자세한 내용을 보려면 [https://www.npmjs.com/](https://www.npmjs.com/) 를 방문 하 여 기본 페이지의 검색 상자에서 패키지 이름을 조회 합니다. 예를 들어 grunt 패키지를 조회 하 여 모든 매개 변수를 설명 하는 설명서 링크를 가져올 수 있습니다.

### <a name="all-together-now"></a>요약정리

Grunt `registerTask()` 메서드를 사용 하 여 특정 순서로 일련의 작업을 실행할 수 있습니다. 예를 들어 위 예제 단계를 정리 > concat-> jshint-> uglify 순서에서 실행 하려면 모듈에 아래 코드를 추가 합니다. InitConfig 외부의 loadNpmTasks () 호출과 같은 수준에 코드를 추가 해야 합니다.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

새 작업은 작업 러너 탐색기의 별칭 작업 아래에 표시 됩니다. 마우스 오른쪽 단추를 클릭 하 고 다른 작업과 마찬가지로 실행할 수 있습니다. `all` 태스크는 `clean`, `concat`, `jshint` 및 `uglify`순서 대로 실행 됩니다.

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>변경 내용 감시하기

`watch` 태스크는 파일 및 디렉터리에 대 한 눈을 유지 합니다. 변경 내용이 검색 되 면 조사식이 자동으로 작업을 트리거합니다. InitConfig에 아래 코드를 추가 하 여 TypeScript 디렉터리에 \*.js 파일의 변경 내용을 감시 합니다. JavaScript 파일이 변경 되 면 `watch`에서 `all` 작업을 실행 합니다.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

`loadNpmTasks()`에 대 한 호출을 추가 하 여 Task Runner 탐색기에서 `watch` 작업을 표시 합니다.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다. 실행 중인 조사식 작업을 보여 주는 명령 창에 "대기 중 ..."이 표시 됩니다. 메시지. TypeScript 파일 중 하나를 열고, 공백을 추가한 다음, 파일을 저장 합니다. 그러면 조사식 태스크가 트리거되고 다른 작업이 순서 대로 실행 되도록 트리거합니다. 아래 스크린샷은 샘플 실행을 보여 줍니다.

![실행 중인 작업 출력](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio 이벤트에 바인딩

Visual Studio에서 작업할 때마다 수동으로 작업을 시작 하려는 경우가 아니면 빌드 **전**, **빌드 후** **정리**및 **프로젝트 열기** 이벤트 이후에 작업을 바인딩합니다.

Visual Studio가 열릴 때마다 실행 되도록 `watch`를 바인딩합니다. 작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **바인딩** > **프로젝트 열기** 를 선택 합니다.

![열려 있는 프로젝트에 작업 바인딩](using-grunt/_static/bindings-project-open.png)

프로젝트를 언로드하고 다시 로드 합니다. 프로젝트가 다시 로드 되 면 조사식 태스크가 자동으로 실행 되기 시작 합니다.

## <a name="summary"></a>요약

Grunt는 대부분의 클라이언트 빌드 작업을 자동화 하는 데 사용할 수 있는 강력한 작업 러너입니다. Grunt는 NPM를 활용 하 여 패키지를 제공 하 고 기능 도구를 Visual Studio와 통합 합니다. Visual Studio의 작업 러너 탐색기는 구성 파일에 대 한 변경 내용을 검색 하 고 작업을 실행 하 고, 실행 중인 작업을 보고, 작업을 Visual Studio 이벤트에 바인딩하는 편리한 인터페이스를 제공 합니다.
