---
title: ASP.NET Core Blazor WebAssembly 호스트 및 배포
author: guardrex
description: ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 861935ff31652f923399a8aa5ae52baa6b77fa91
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172403"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly 호스팅 모델 사용](xref:blazor/hosting-models#blazor-webassembly):

* Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.
* 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.

다음 배포 전략이 지원됩니다.

* Blazor 앱은 ASP.NET Core 앱에서 제공됩니다. 이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.
* Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다. 이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.

## <a name="rewrite-urls-for-correct-routing"></a>올바른 라우팅을 위해 URL 다시 생성

Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor 서버에서 호스트한 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다. 다음 두 가지 구성 요소가 있는 Blazor WebAssembly 앱을 생각해 보겠습니다.

* *Main.razor* &ndash; 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.
* *About.razor* &ndash; `About` 구성 요소입니다.

브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):

1. 브라우저가 요청을 합니다.
1. 기본 페이지(일반적으로 *index.html*)가 반환됩니다.
1. *index.html* 앱을 부트스트랩으로 처리합니다.
1. Blazor의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.

Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다. *Blazor WebAssembly 앱 내*의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다. 라우터가 내부적으로 요청을 처리합니다.

브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다. 앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.

브라우저는 인터넷 기반 호스트에 대해 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 *index.html* 페이지에 씁니다. *index.html*이 반환되는 경우 앱의 Blazor 라우터가 인수하여 올바른 리소스로 응답합니다.

IIS 서버에 배포하는 경우 앱의 게시된 *web.config* 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다. 자세한 내용은 [IIS](#iis) 섹션을 참조하세요.

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core를 사용하여 호스트된 배포

*호스트된 배포*는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.

Blazor 앱은 ASP.NET Core 앱과 함께 배포되도록 게시된 출력의 후자 앱과 함께 포함됩니다. ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다. 호스트된 배포의 경우 Visual Studio에는 **Hosted** 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)이 포함됩니다.

ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.

Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.

## <a name="standalone-deployment"></a>독립 실행형 배포

*독립 실행형 배포*는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다. 모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.

독립 실행형 배포 자산은 *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* 폴더에 게시됩니다.

### <a name="iis"></a>IIS

IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다. Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.

게시된 자산은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 생성됩니다. 웹 서버 또는 호스팅 서비스에서 *publish* 폴더의 콘텐츠를 호스트합니다.

#### <a name="webconfig"></a>web.config

Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 *web.config* 파일이 생성됩니다.

* 다음 파일 확장명에 대해 MIME 형식을 설정합니다.
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* 다음 MIME 형식에 대해 HTTP 압축을 사용합니다.
  * `application/octet-stream`
  * `application/wasm`
* URL 재작성 모듈 규칙을 설정합니다.
  * 앱의 정적 자산이 상주하는 하위 디렉터리( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* )를 제공합니다.
  * 파일이 아닌 자산에 대한 요청이 정적 자산 폴더에 있는 앱의 기본 문서( *{ASSEMBLY NAME}/dist/index.html*)로 리디렉션되도록 SPA 폴백 라우팅을 만듭니다.

#### <a name="install-the-url-rewrite-module"></a>URL 재작성 모듈 설치

URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다. 이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다. 이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다. 웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.

1. 로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다. 영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다. 다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.
1. 설치 관리자를 서버에 복사합니다. 설치 관리자를 실행합니다. **설치** 버튼을 선택하고 사용 조건에 동의합니다. 설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.

#### <a name="configure-the-website"></a>웹 사이트 구성

웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다. 이 폴더는 다음을 포함합니다.

* 필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 *web.config* 파일.
* 앱의 정적 자산 폴더입니다.

#### <a name="host-as-an-iis-sub-app"></a>IIS 하위 앱으로 호스트

독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.

* 상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.

  Blazor 앱의 게시된 *web.config* 파일에 `<handlers>` 섹션을 추가하여 파일에서 핸들러를 제거합니다.

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:host-and-deploy/blazor/index#app-base-path)에 더하여 수행됩니다. IIS에서 하위 앱을 구성할 때
앱의 *index.html* 파일에서 앱 기본 경로를 사용한 IIS 별칭으로 설정합니다.

#### <a name="troubleshooting"></a>문제 해결

500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.  모듈이 설치되지 않은 경우 IIS가 *web.config* 파일을 구문 분석할 수 없습니다. 그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.

IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다. 사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.

스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:

* **인덱스 문서 이름**을 `index.html`로 설정합니다.
* **오류 문서 경로**를 `index.html`로 설정합니다. Razor 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob service에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다. 이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다. *index.html* BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.

자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.

### <a name="nginx"></a>Nginx

다음 *nginx.conf* 파일은 Nginx가 디스크에서 *index.html* 파일을 찾을 수 없을 때마다 해당 파일을 보내도록 구성하는 방법을 보여 주도록 단순화되었습니다.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.

### <a name="nginx-in-docker"></a>Docker의 Nginx

Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다. Dockerfile을 업데이트하여 *nginx.config* 파일을 컨테이너에 복사합니다.

다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Blazor WebAssembly 앱을 CentOS 7 이상에 배포하려면 다음을 수행합니다.

1. Apache 구성 파일을 만듭니다. 다음 예제는 단순화된 구성 파일입니다(*blazorapp.config*).

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType aplication/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.

1. 앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.

1. Apache 서비스를 다시 시작합니다.

자세한 내용은 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)를 참조하세요.

### <a name="github-pages"></a>GitHub 페이지

URL 다시 생성을 처리하려면 *index.html* 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 *404.html* 파일을 추가합니다. 커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](https://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요. 커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.

조직의 사이트 대신 프로젝트 사이트를 사용하는 경우 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다. `href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).

## <a name="host-configuration-values"></a>호스트 구성 값

[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.

### <a name="content-root"></a>콘텐츠 루트

`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다. 다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다. 앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>경로 기준

`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.) 다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다. 자세한 내용은 [앱 기본 경로](xref:host-and-deploy/blazor/index#app-base-path)를 참조하세요.

> [!IMPORTANT]
> `<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다. 앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다. 앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다. 앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>링커 구성

Blazor는 각 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다. 어셈블리 연결은 빌드 시 제어할 수 있습니다. 자세한 내용은 <xref:host-and-deploy/blazor/configure-linker>를 참조하세요.
