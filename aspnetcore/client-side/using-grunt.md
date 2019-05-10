---
title: ASP.NET Core에서 사용 하 여 Grunt
author: rick-anderson
description: ''
ms.author: riande
ms.date: 10/14/2016
uid: client-side/using-grunt
ms.openlocfilehash: fc912974fb6ed3c65bb46a7d616d9e531587d946
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894640"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core에서 사용 하 여 Grunt

[Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt은 스크립트 축소, TypeScript 컴파일, 코드 품질 다름이 도구, 프로세서를 전 하는 CSS, 및 클라이언트 개발을 지원 하기 위해 수행 해야 하는 모든 반복 작업에 대 한 자동화 하는 JavaScript 작업 실행 기는 합니다. ASP.NET 프로젝트 템플릿은 기본적으로 Gulp를 사용 하지만 Visual Studio에서 grunt 완전히 지원 됩니다 (참조 [Gulp 사용](using-gulp.md)).

이 예제에서는 빈 ASP.NET Core 프로젝트의 시작 점으로에서 클라이언트 빌드 프로세스를 자동화 하는 방법을 보여 줍니다.

완성 된 예제에서는 대상 배포 디렉터리를 정리, JavaScript 파일을 결합, 코드 품질을 확인, JavaScript 파일 콘텐츠를 압축 및 웹 응용 프로그램의 루트에 배포 합니다. 다음 패키지를 사용 합니다.

* **grunt**: Grunt 작업 실행 기 패키지입니다.

* **grunt-contrib-clean**: 파일 또는 디렉터리를 제거 하는 플러그 인입니다.

* **grunt-contrib-jshint**: JavaScript 코드 품질을 검토 하는 플러그 인입니다.

* **grunt-contrib-concat**: 단일 파일에 파일을 조인 하는 플러그 인입니다.

* **grunt-contrib-uglify**: 크기를 줄이기 위해 JavaScript를 축소 하는 플러그 인입니다.

* **grunt-contrib-watch**: 파일 작업을 감시 하는 플러그 인입니다.

## <a name="preparing-the-application"></a>응용 프로그램 준비

시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예제 파일을 추가 합니다. TypeScript 파일 자동으로 기본 Visual Studio 설정을 사용 하 여 JavaScript로 컴파일되는 및 Grunt를 사용 하 여 처리 하는 데는 원료 됩니다.

1. Visual Studio에서 만드는 새 `ASP.NET Web Application`합니다.

2. 에 **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET Core를 선택 **빈** 템플릿을 확인 단추를 클릭 합니다.

3. 솔루션 탐색기에서 프로젝트 구조를 검토 합니다. 합니다 `\src` 폴더에 빈 포함 `wwwroot` 및 `Dependencies` 노드.

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. 라는 새 폴더 추가 `TypeScript` 프로젝트 디렉터리에 있습니다.

5. 모든 파일을 추가 하기 전에 Visual Studio 옵션에 있는지 확인 ' 컴파일 저장 시 ' 체크 TypeScript 파일에 대 한 합니다. 이동할 **도구가** > **옵션** > **텍스트 편집기** > **Typescript**  >  **프로젝트**:

    ![TypeScript 파일 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. 마우스 오른쪽 단추로 클릭 합니다 `TypeScript` 디렉터리 및 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다. 선택 된 **JavaScript 파일** 항목 및 파일 이름을 *Tastes.ts* (참고는 \*.ts 확장). 아래 TypeScript 코드 줄을 파일에 복사 (저장할 때, 새 *Tastes.js* JavaScript 소스 파일이 표시 됩니다).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 두 번째 파일을 추가 합니다 **TypeScript** 디렉터리 이름을 `Food.ts`입니다. 파일에 아래 코드를 복사 합니다.

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

다음으로, grunt 및 grunt 작업을 다운로드 하려면 NPM을 구성 합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다. 선택는 **NPM 구성 파일** 항목, 기본 이름을 그대로 *package.json*를 클릭 합니다 **추가** 단추입니다.

2. 에 *package.json* 파일에 `devDependencies` 중괄호 개체 "grunt"를 입력 합니다. 선택 `grunt` Intellisense에서 나열 하 고 Enter 키를 누릅니다. Visual Studio grunt 패키지 이름을 따옴표를 콜론을 추가 합니다. 콜론의 오른쪽에 Intellisense 목록 맨 위에서 패키지의 안정적인 최신 버전 선택 (키를 눌러 `Ctrl-Space` Intellisense 표시 하지 않는 경우).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM을 사용 [유의 적 버전](http://semver.org/) 종속성을 구성 합니다. 번호 매기기 체계를 사용 하 여 패키지를 식별 하는 의미 체계 버전 관리, 라고도 SemVer \<주요 >.\< 부 버전 >. \<패치 >. Intellisense만 몇 가지 일반적인 옵션을 표시 하 여 의미 체계 버전 관리를 간소화 합니다. (위의 예에서 0.4.5) Intellisense 목록에 맨 위 항목에는 패키지의 안정적인 최신 버전으로 간주 됩니다. 캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 항목과 물결표 (~) 최신 부 버전. 참조 된 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) SemVer 제공 하는 전체 표현 가이드로 합니다.

3. 로드에 대 한 자세한 종속성 grunt 추가-contrib-\* 에 대 한 패키지 *정리*를 *jshint*를 *concat*를 *uglify*, 및 *조사식* 아래 예와에서 같이 합니다. 버전은 예제와 일치 하지 않아도 됩니다.

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

4. 저장 된 *package.json* 파일입니다.

각 패키지에 필요한 모든 파일과 각 devDependencies 항목에 대 한 패키지를 다운로드 합니다. 패키지 파일을 찾을 수 있습니다 합니다 `node_modules` 사용 하 여 디렉터리를 **모든 파일 표시** 솔루션 탐색기에서 단추입니다.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 솔루션 탐색기에서 종속성 마우스 오른쪽 단추로 클릭 하 여 수동으로 복원할 수 해야 할 경우 `Dependencies\NPM` 를 선택 하 고는 **패키지 복원** 메뉴 옵션입니다.

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt 구성

Grunt 이라는 매니페스트를 사용 하 여 구성 됩니다 *Gruntfile.js* 정의 하는, 로드 하 고 수동으로 실행 하거나 Visual Studio에서 이벤트에 따라 자동으로 실행 되도록 구성할 수 있는 작업을 등록 합니다.

1. 프로젝트를 마우스 오른쪽 단추로 누르고 **추가 > 새 항목**합니다. 선택는 **Grunt 구성 파일** 옵션에서 기본 이름을 그대로 *Gruntfile.js*를 클릭 합니다 **추가** 단추.

   모듈 정의 포함 하는 초기 코드 및 `grunt.initConfig()` 메서드. `initConfig()` 각 패키지에 대 한 옵션을 설정 하는 모듈의 나머지 부분을 작업 등록 및 로드 됩니다.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. 내부를 `initConfig()` 메서드를에 대 한 옵션을 추가 합니다 `clean` 예제에 표시 된 대로 작업 *Gruntfile.js* 아래. Clean 작업이 디렉터리 문자열 배열을 허용합니다. 이 태스크는 wwwroot/lib에서 파일을 제거 하 고 전체/임시 디렉터리를 제거 합니다.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. 아래 initConfig() 메서드 호출을 추가 `grunt.loadNpmTasks()`합니다. 이렇게 하면 작업 실행 가능한 Visual Studio에서.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. 저장할 *Gruntfile.js*합니다. 파일은 아래 스크린샷과 같은 비슷해야 합니다.

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. 마우스 오른쪽 단추로 클릭 *Gruntfile.js* 선택한 **Task Runner 탐색기** 상황에 맞는 메뉴입니다. Task Runner 탐색기 창이 열립니다.

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

6. 확인 `clean` 아래에 표시 **작업** Task Runner 탐색기에 있습니다.

    ![작업 러너 탐색기 태스크 목록](using-grunt/_static/task-runner-explorer-tasks.png)

7. 정리 작업을 마우스 오른쪽 단추로 클릭 **실행** 상황에 맞는 메뉴입니다. 명령 창에는 작업의 진행률이 표시 됩니다.

    ![작업 러너 탐색기 실행 정리 작업](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 파일 또는 디렉터리가 아직 정리에 있습니다. 원하는 경우 솔루션 탐색기에서이 수동으로 만들 수 있으며 다음 테스트로 정리 작업을 실행 수 있습니다.

8. InitConfig() 메서드에서 추가 대 한 항목이 `concat` 아래 코드를 사용 합니다.

    `src` 속성 배열 이러한 결합 해야 순서로 결합 하는 파일을 나열 합니다. `dest` 속성 생성 되는 결합된 된 파일에 경로 할당 합니다.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all` 위의 코드에서 속성은 대상의 이름입니다. 목표는 여러 빌드 환경 수 있도록 일부 Grunt 작업에 사용 됩니다. Intellisense를 사용 하 여 기본 제공 대상을 보거나 직접 지정할 수 있습니다.

9. 추가 된 `jshint` 아래 코드를 사용 하 여 작업 합니다.

    Jshint 코드 품질 유틸리티는 임시 디렉터리에 있는 모든 JavaScript 파일에 대해 실행 됩니다.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 옵션 "-W069"는 오류가 때 생성 한 jshint JavaScript는 괄호, 즉 점 표기법 대신 속성을 할당 하는 구문을 `Tastes["Sweet"]` 대신 `Tastes.Sweet`합니다. 옵션을 계속 하는 프로세스의 나머지 부분을 허용 하도록 경고를 해제 합니다.

10. 추가 된 `uglify` 아래 코드를 사용 하 여 작업 합니다.

    축소 작업은 *combined.js* 파일이 임시 디렉터리에서 찾은 wwwroot/lib 표준 명명 규칙에 따라에 결과 파일을 만듭니다  *\<파일 이름\>. min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. Grunt contrib 정리를 로드 하는 호출 grunt.loadNpmTasks(), jshint, concat에 대 한 동일한 호출을 포함 한 uglify 아래 코드를 사용 합니다.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. 저장할 *Gruntfile.js*합니다. 파일은 아래 예제와 비슷해야 합니다.

    ![전체 grunt 파일 예제](using-grunt/_static/gruntfile-js-complete.png)

13. 작업 러너 탐색기 태스크 목록에 포함 `clean`, `concat`, `jshint` 고 `uglify` 작업. 순서로 각 태스크를 실행 하 고 솔루션 탐색기에서 결과 관찰 합니다. 각 태스크는 오류 없이 실행 해야 합니다.

    ![각 태스크를 실행 하는 task runner 탐색기](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat 태스크를 만듭니다 *combined.js* 파일을 임시 디렉터리에 배치 합니다. 단순히 jshint 작업 실행 하 고 출력을 생성 하지 않습니다. Uglify 태스크를 만듭니다 *combined.min.js* 파일을 wwwroot/lib에 넣습니다. 완료 되 면 솔루션 아래 스크린샷과 비슷하게 표시 됩니다.

    ![솔루션 탐색기에서 모든 작업](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 각 패키지에 대 한 옵션에 대 한 자세한 내용은 방문 [ https://www.npmjs.com/ ](https://www.npmjs.com/) 및 기본 페이지에서 검색 상자에 패키지 이름 조회 합니다. 예를 들어, 모든 매개 변수를 설명 하는 설명서 링크를 표시 하려면 grunt contrib 정리 패키지를 조회할 수 있습니다.

### <a name="all-together-now"></a>모두 통합

Grunt를 사용 하 여 `registerTask()` 특정 순서로 일련의 작업을 실행 하는 방법입니다. 예를 들어, 위의 단계를 정리 하는 순서로-> 예제를 실행 하려면 concat-> jshint-> uglify 모듈에 아래 코드를 추가 합니다. 코드는 initConfig 외부 loadNpmTasks() 호출에 동일한 수준에 추가 되어야 합니다.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

별칭 작업에서 Task Runner 탐색기에서 새 작업이 표시 됩니다. 마우스 오른쪽 단추로 클릭 하 고 다른 작업 처럼 실행할 수 있습니다. `all` 태스크가 실행 됩니다 `clean`, `concat`, `jshint` 및 `uglify`를 순서 대로 합니다.

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>변경 내용 감시하기

`watch` 작업 파일 및 디렉터리를 유지 합니다. 시계 변경 내용을 감지한 경우 자동으로 작업을 트리거합니다. 아래 코드에 변경 내용을 감시 하려면 initConfig 추가할 \*TypeScript 디렉터리의.js 파일입니다. JavaScript 파일이 변경 되 면 `watch` 실행될지는 `all` 작업 합니다.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

호출을 추가 `loadNpmTasks()` 표시할는 `watch` Task Runner 탐색기에서 작업 합니다.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Task Runner 탐색기에 있는 watch 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다. "대기 중..."이 실행 중인 보기 작업을 보여 주는 명령 창에 표시 됩니다. 메시지. TypeScript 파일 중 하나를 열고 하 고 공백을 추가 파일을 저장 합니다. 조사식 작업 트리거 되 고 순서 대로 실행 하는 다른 작업을 트리거합니다. 다음 스크린샷은 샘플 실행을 보여 줍니다.

![작업 출력을 실행](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio 이벤트에 바인딩

Visual Studio에서 작업할 때마다 작업을 수동으로 시작 하려는 경우가 아니면 작업을 바인딩할 수 있습니다 **하기 전에 빌드**, **후 빌드**합니다 **정리**, 및  **프로젝트 열기** 이벤트입니다.

바인딩 하겠습니다 `watch` Visual Studio가 열릴 때마다 실행 되도록 합니다. Task Runner 탐색기에서 보기 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **바인딩 > 프로젝트 열기** 상황에 맞는 메뉴입니다.

![프로젝트 열기 작업을 바인딩합니다](using-grunt/_static/bindings-project-open.png)

언로드하고 프로젝트를 다시 로드 합니다. 프로젝트 다시 로드 되 면 조사식 작업 실행을 자동으로 시작 됩니다.

## <a name="summary"></a>요약

Grunt를 강력한 작업 실행 기 대부분의 클라이언트 빌드 작업을 자동화 하는 경우 Grunt NPM의 패키지 및 Visual Studio와의 통합 도구 기능 제공을 활용 합니다. Visual Studio의 Task Runner 탐색기 구성 파일의 변경 사항을 감지 하 고 작업을 실행 하 고 실행 중인 작업을 보고 Visual Studio 이벤트에 작업을 바인딩할 하는 편리한 인터페이스를 제공 합니다.

## <a name="additional-resources"></a>추가 자료

* [Gulp 사용하기](using-gulp.md)
