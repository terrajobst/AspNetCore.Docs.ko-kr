---
title: ASP.NET Core에서 Bower 사용 하 여 클라이언트 쪽 패키지 관리
author: rick-anderson
description: Bower 사용 하 여 클라이언트 쪽 패키지를 관리 합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813524"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>ASP.NET Core에서 Bower 사용 하 여 클라이언트 쪽 패키지 관리

하 여 [Rick Anderson](https://twitter.com/RickAndMSFT)하십시오 [Noel Rice](https://twitter.com/noelrice1), 및 [Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> Bower을 유지 하는 동안 해당 유지 관리자는 다른 솔루션을 사용 하 여 권장 합니다. [라이브러리 관리자](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (줄여서 LibMan)은 Visual Studio의 새 클라이언트 쪽 라이브러리 가져오기 도구 (Visual Studio 15.8 이상). 자세한 내용은 <xref:client-side/libman/index>을 참조하세요. Bower 버전 15.5 통해 Visual Studio에서 지원 됩니다.
>
> Webpack 사용 하 여 yarn 되는 인기 있는 한 가지 대안은 [마이그레이션 지침](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) 사용할 수 있습니다.

[Bower](https://bower.io/) "웹에 대 한 패키지 관리자" 자신을 호출 합니다. .NET 에코 시스템 내에서 NuGet의 정적 콘텐츠 파일을 배달 하지 못하는 남긴 void를 채우도록 합니다. ASP.NET Core 프로젝트에 대 한 이러한 정적 파일은 같은 클라이언트 쪽 라이브러리에 내재 [jQuery](https://jquery.com/) 하 고 [부트스트랩](https://getbootstrap.com/)합니다. .NET 라이브러리에 대해 여전히 사용 [NuGet](https://www.nuget.org/) 패키지 관리자입니다.

클라이언트 쪽 설정 하는 ASP.NET Core 프로젝트 템플릿을 사용 하 여 만든 새로운 프로젝트는 프로세스를 빌드합니다. [jQuery](https://jquery.com/) 하 고 [부트스트랩](https://getbootstrap.com/) 설치할지, Bower 지원 됩니다.

클라이언트 쪽 패키지에 나열 됩니다는 *bower.json* 파일입니다. ASP.NET Core 프로젝트 템플릿 구성 *bower.json* jQuery, jQuery 유효성 검사 및 부트스트랩 합니다.

이 자습서에서는 대 한 지원을 추가 했습니다 [Font Awesome](http://fontawesome.io)합니다. Bower 패키지를 사용 하 여 설치할 수는 **Bower 패키지 관리** UI 또는 수동으로 합니다 *bower.json* 파일입니다.

### <a name="installation-via-manage-bower-packages-ui"></a>Bower 패키지 관리 UI 통해 설치

* 사용 하 여 새 ASP.NET Core 웹 앱 만들기를 **ASP.NET Core 웹 응용 프로그램 (.NET Core)** 템플릿. 선택 **웹 응용 프로그램** 하 고 **인증 안 함**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 누르고 **Bower 패키지 관리** (또는 주 메뉴에서 **프로젝트** > **Bower패키지관리**).

* 에 **Bower: \<프로젝트 이름\>**  창에서 "찾아보기" 탭을 클릭 한 다음을 입력 하 여 패키지 목록 필터링 `font-awesome` 검색 상자에서:

  ![bower 패키지 관리](bower/_static/manage-bower-packages.png)

* 있는지 확인 합니다 "의 변경 내용을 저장할 *bower.json*" 확인란을 선택 합니다. 드롭다운 목록에서 버전을 선택 하 고 클릭 합니다 **설치** 단추입니다. 합니다 **출력** 창 설치 세부 정보를 표시 합니다.

### <a name="manual-installation-in-bowerjson"></a>Bower.json에 수동 설치

엽니다는 *bower.json* 파일 및 종속성에 "글꼴 탁월한"를 추가 합니다. IntelliSense에 사용할 수 있는 패키지를 보여 줍니다. 패키지를 선택 하면 사용 가능한 버전 표시 됩니다. 아래 이미지에서 오래 된 및 표시 되는 내용 일치 하지 않습니다.

![Bower 패키지 탐색기의 IntelliSense](bower/_static/add-package.png)

![bower 버전 IntelliSense](bower/_static/version-intelliSense.png)

사용 하 여 bower [의미 체계 버전 관리](https://semver.org/) 종속성을 구성 합니다. 번호 매기기 체계를 사용 하 여 패키지를 식별 하는 의미 체계 버전 관리, 라고도 SemVer \<주요 >.\< 부 버전 >. \<패치 >. IntelliSense만 몇 가지 일반적인 옵션을 표시 하 여 의미 체계 버전 관리를 간소화 합니다. (위의 예에서 4.6.3) IntelliSense 목록에 맨 위 항목에는 패키지의 안정적인 최신 버전으로 간주 됩니다. 캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 항목과 물결표 (~) 최신 부 버전.

저장 된 *bower.json* 파일입니다. Visual Studio를 감시 합니다 *bower.json* 변경에 대 한 파일입니다. 저장 시 합니다 *bower 설치* 명령을 실행 합니다. 출력 창을 참조 하십시오 **Bower/npm** 실행 되는 정확한 명령에 대 한 보기입니다.

엽니다는 *.bowerrc* 파일 *bower.json*합니다. 합니다 `directory` 속성이 *wwwroot/lib* Bower 위치를 나타내는 패키지 자산을 설치 합니다.

```json
{
 "directory": "wwwroot/lib"
}
```

찾아 글꼴 멋진 패키지를 표시 하려면 솔루션 탐색기에서 검색 상자를 사용할 수 있습니다.

엽니다는 *Views\Shared\_Layout.cshtml* 파일 및 환경 글꼴 멋진 CSS 파일에 추가할 [태그 도우미](xref:mvc/views/tag-helpers/intro) 에 대 한 `Development`합니다. 솔루션 탐색기에서 끌어서 놓기 *글꼴 awesome.css* 안에 `<environment names="Development">` 요소입니다.

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

프로덕션 앱에 추가 *글꼴 awesome.min.css* 에 대 한 환경 태그 도우미에 `Staging,Production`입니다.

내용을 대체 합니다 *Views\Home\About.cshtml* 다음 태그를 사용 하 여 Razor 파일:

[!code-html[](bower/sample/About.cshtml)]

앱을 실행 하 고 글꼴 멋진 패키지 작동 하는지 확인 하 고 About 뷰로 이동 합니다.

## <a name="exploring-the-client-side-build-process"></a>클라이언트 쪽 빌드 프로세스를 탐색합니다.

대부분의 ASP.NET Core 프로젝트 템플릿에 이미 Bower를 사용 하도록 구성 됩니다. 빈 ASP.NET Core 프로젝트를 시작 하 고 프로젝트에 Bower 사용 방법에 대 한 느낌을 받을 수 있도록 각 부분을 수동으로 추가 하는이 다음 연습 합니다. 프로젝트 구조 및 각 구성을 변경 하는 대로 출력 런타임으로 어떻게 확인할 수 있습니다.

Bower를 사용 하 여 클라이언트 쪽 빌드 프로세스를 사용 하는 일반적인 단계는:

* 프로젝트에서 사용 되는 패키지를 정의 합니다. <!-- once defined, you don't need to download them, VS does -->
* 웹 페이지에서 패키지를 참조 합니다.

### <a name="define-packages"></a>패키지 정의

패키지를 나열 되 면 합니다 *bower.json* 파일을 Visual Studio 다운로드 됩니다. Bower를 사용 하 여 jQuery 및 Bootstrap을 로드 하는 다음 예제는 *wwwroot* 폴더입니다.

* 사용 하 여 새 ASP.NET Core 웹 앱 만들기를 **ASP.NET Core 웹 응용 프로그램 (.NET Core)** 템플릿. 선택 된 **빈** 프로젝트 템플릿 및 클릭 **확인**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **새 항목 추가** 선택한 **Bower 구성 파일**합니다. 참고: A *.bowerrc* 파일도 추가 됩니다.

* 열기 *bower.json*, 및 jquery를 추가 하 고를 부트스트랩 합니다 `dependencies` 섹션입니다. 결과 *bower.json* 파일은 다음과 같이 표시 됩니다. 버전은 시간이 지남에 따라 변경 되 고 아래 이미지와 일치 하지 않을 수 있습니다.

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* 저장 된 *bower.json* 파일입니다.

  프로젝트에 포함 되어 있는지 확인 합니다 *부트스트랩* 하 고 *jQuery* 디렉터리 *wwwroot/lib*합니다. 사용 하 여 bower 합니다 *.bowerrc* 자산을 설치 하는 파일 *wwwroot/lib*합니다.

  참고: "Bower 패키지 관리" UI 파일 수동 편집으로 대안을 제공합니다.

### <a name="enable-static-files"></a>정적 파일을 사용 하도록 설정

* 추가 된 `Microsoft.AspNetCore.StaticFiles` 프로젝트에 NuGet 패키지.
* 사용 하 여 처리할 수 있는 정적 파일을 사용 하도록 설정 합니다 [정적 파일 미들웨어](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)합니다. 호출을 추가 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) 에 `Configure` 메서드의 `Startup`합니다.

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>참조 패키지

이 섹션에서는 배포 된 패키지에 액세스할 수를 확인 하려면 HTML 페이지를 만듭니다.

* 라는 새 HTML 페이지 추가 *Index.html* 에 *wwwroot* 폴더입니다. 참고: HTML 파일을 추가 해야 합니다 *wwwroot* 폴더입니다. 기본적으로 정적 콘텐츠를 처리할 수 없는 외부 *wwwroot*합니다. 참조 [정적 파일](xref:fundamentals/static-files) 자세한 내용은 합니다.

  내용을 바꿉니다 *Index.html* 다음 태그를 사용 하 여:

[!code-html[](bower/sample/Index.html)]

* 앱을 실행 하 고 이동 `http://localhost:<port>/Index.html`합니다. 사용 하 여 또는 *Index.html* 키를 눌러 열 `Ctrl+Shift+W`합니다. Jumbotron 스타일이 적용 되는 jQuery 코드는 단추를 클릭할 때 응답 하 고 부트스트랩 단추 상태가 변경 되는지 확인 합니다.

  ![jumbotron 스타일 적용](bower/_static/jumbotron.png)
