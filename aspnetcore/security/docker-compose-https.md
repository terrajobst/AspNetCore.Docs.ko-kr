---
title: HTTPS를 사용하여 작성된 docker를 사용하여 컨테이너에서 ASP.NET 코어 이미지 호스팅
author: ravipal
description: HTTPS를 통해 Docker 구성을 사용하여 ASP.NET 핵심 이미지를 호스팅하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381811"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>HTTPS를 통해 Docker 작성을 통해 ASP.NET 코어 이미지 호스팅


ASP.NET 코어는 [기본적으로 HTTPS를](/aspnet/core/security/enforcing-ssl)사용합니다. [HTTPS는](https://en.wikipedia.org/wiki/HTTPS) 신뢰, ID 및 암호화를 위해 [인증서를](https://en.wikipedia.org/wiki/Public_key_certificate) 사용합니다.

이 문서에서는 HTTPS를 사용하여 미리 빌드된 컨테이너 이미지를 실행하는 방법을 설명합니다.

개발 시나리오에 대한 [HTTPS를 통해 Docker를 통해 ASP.NET 핵심 응용 프로그램 개발을](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) 참조하십시오.

이 샘플에는 [Docker 클라이언트의](https://www.docker.com/products/docker) [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) 이상이 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 일부 지침에는 [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) 이상이 필요합니다.

## <a name="certificates"></a>인증서

도메인에 대한 [프로덕션 호스팅에는](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) [인증 기관의](https://wikipedia.org/wiki/Certificate_authority) 인증서가 필요합니다. [Let's Encrypt](https://letsencrypt.org/)은 무료 인증서를 제공하는 인증 기관입니다.

이 문서에서는 미리 빌드된 이미지를 호스팅하기 위해 `localhost` [자체 서명된 개발 인증서를](https://wikipedia.org/wiki/Self-signed_certificate) 사용합니다. 지침은 프로덕션 인증서를 사용하는 것과 유사합니다.

생산 인증서의 경우:

* 이 `dotnet dev-certs` 도구는 필요하지 않습니다.
* 인증서는 지침에 사용된 위치에 저장할 필요가 없습니다. 인증서는 사이트 디렉터리 외부의 모든 위치에 저장합니다.

다음 섹션 볼륨에 포함된 지침은 `volumes` *docker-compose.yml의* 속성을 사용하여 인증서를 컨테이너에 탑재합니다. `COPY` *Dockerfile에*명령을 가진 컨테이너 이미지에 인증서를 추가할 수 있지만 권장되지는 않습니다. 다음과 같은 이유로 인증서를 이미지에 복사하는 것은 권장되지 않습니다.

* 개발자 인증서를 사용하여 테스트할 때 동일한 이미지를 사용하기가 어렵습니다.
* 프로덕션 인증서를 사용하여 호스팅에 동일한 이미지를 사용하기가 어렵습니다.
* 인증서 공개의 위험이 큽습니다.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>docker 작성을 사용하여 https 지원으로 컨테이너 시작

운영 체제 구성에 대해 다음 지침을 사용합니다.

### <a name="windows-using-linux-containers"></a>리눅스 컨테이너를 사용 하 여 윈도우

인증서를 생성하고 로컬 컴퓨터를 구성합니다.

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

앞의 명령에서 암호로 `{ password here }` 바꿉습니다.

다음 내용으로 _docker-compose.debug.yml_ 파일을 만듭니다.

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
docker 작성 파일에 지정된 암호는 인증서에 사용되는 암호와 일치해야 합니다.

https를 위해 구성된 ASP.NET 코어로 컨테이너를 시작합니다.

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>맥 OS 또는 리눅스

인증서를 생성하고 로컬 컴퓨터를 구성합니다.

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`macOS 및 Windows에서만 지원됩니다. 당신은 당신의 배포판에 의해 지원되는 방식으로 리눅스에서 인증서를 신뢰해야합니다. 브라우저에서 인증서를 신뢰해야 할 수 있습니다.

앞의 명령에서 암호로 `{ password here }` 바꿉습니다.

다음 내용으로 _docker-compose.debug.yml_ 파일을 만듭니다.

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
docker 작성 파일에 지정된 암호는 인증서에 사용되는 암호와 일치해야 합니다.

https를 위해 구성된 ASP.NET 코어로 컨테이너를 시작합니다.

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>윈도우 컨테이너를 사용하는 윈도우

인증서를 생성하고 로컬 컴퓨터를 구성합니다.

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

앞의 명령에서 암호로 `{ password here }` 바꿉습니다.

다음 내용으로 _docker-compose.debug.yml_ 파일을 만듭니다.

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
docker 작성 파일에 지정된 암호는 인증서에 사용되는 암호와 일치해야 합니다.

https를 위해 구성된 ASP.NET 코어로 컨테이너를 시작합니다.

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
