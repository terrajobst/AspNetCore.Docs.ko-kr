---
title: Dotnet grpc를 사용 하 여 Protobuf 참조 관리
author: juntaoluo
description: Dotnet grpc global 도구를 사용 하 여 Protobuf 참조를 추가, 업데이트, 제거 및 나열 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/24/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: ebd57419be24f7f4ed9765e36cf14189be8438b1
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290048"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Dotnet grpc를 사용 하 여 Protobuf 참조 관리

작성자: [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`은 .NET gRPC 프로젝트 내에서 Protobuf 참조를 관리 하기 위한 .NET Core 전역 도구입니다. 도구를 사용 하 여 Protobuf 참조를 추가, 새로 고침, 제거 및 나열할 수 있습니다.

## <a name="installation"></a>설치

@No__t-0 [.Net Core 글로벌 도구](/dotnet/core/tools/global-tools)를 설치 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>참조 추가

`dotnet-grpc`은 *.csproj* 파일에 `<Protobuf />` 항목으로 Protobuf references를 추가 하는 데 사용할 수 있습니다.

```xml
<Protobuf Include="..\Proto\count.proto" GrpcServices="Server" Link="Protos\count.proto" />
```

Protobuf 참조는 C# 클라이언트 및/또는 서버 자산을 생성 하는 데 사용 됩니다. @No__t-0tool은 다음을 수행할 수 있습니다.

* 디스크의 로컬 파일에서 Protobuf 참조를 만듭니다.
* URL로 지정 된 원격 파일에서 Protobuf 참조를 만듭니다.
* 올바른 gRPC 패키지 종속성이 프로젝트에 추가 되었는지 확인 합니다.

예를 들어 `Grpc.AspNetCore` 패키지는 웹 앱에 추가 됩니다. `Grpc.AspNetCore`에는 gRPC 서버 및 클라이언트 라이브러리와 도구 지원이 포함 됩니다. 또는 gRPC 클라이언트 라이브러리 및 도구 지원만 포함 하는 @no__t 0, `Grpc.Tools` 및 @no__t 2 패키지가 콘솔 앱에 추가 됩니다.

### <a name="add-file"></a>파일 추가

@No__t-0 명령은 디스크의 로컬 파일을 Protobuf references로 추가 하는 데 사용 됩니다. 제공 된 파일 경로:

* 현재 디렉터리나 절대 경로에 대 한 상대 경로일 수 있습니다.
* 패턴 기반 파일 [와일드 카드 사용](https://wikipedia.org/wiki/Glob_(programming))와일드 카드를 포함할 수 있습니다.

파일이 프로젝트 디렉터리 외부에 있는 경우 Visual Studio에서-1 @no__t 폴더 아래에 파일을 표시 하는 `Link` 요소가 추가 됩니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| files | Protobuf 파일 참조입니다. 로컬 protobuf 파일의 glob에 대 한 경로일 수 있습니다. |

#### <a name="options"></a>변수

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.
| -S | --서비스 | 생성 해야 하는 gRPC 서비스의 유형입니다. @No__t-0이 지정 된 경우 웹 프로젝트에 `Both`이 사용 되 고 웹이 아닌 프로젝트에는 `Client`가 사용 됩니다. 허용 되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.
| -i | --추가 가져오기-디렉터리 | Protobuf 파일에 대 한 가져오기를 확인할 때 사용할 추가 디렉터리입니다. 세미콜론으로 구분 된 경로 목록입니다.
| | --액세스 | 생성 C# 된 클래스에 사용할 액세스 한정자입니다. 기본값은 `Public`입니다. 허용 되는 값은 0 @no__t `Public`입니다.

### <a name="add-url"></a>URL 추가

@No__t-0 명령은 원본 URL에서 지정 된 원격 파일을 Protobuf reference로 추가 하는 데 사용 됩니다. 원격 파일을 다운로드할 위치를 지정 하려면 파일 경로를 제공 해야 합니다. 파일 경로는 현재 디렉터리 또는 절대 경로에 대 한 상대 경로일 수 있습니다. 파일 경로가 프로젝트 디렉터리 외부에 있는 경우 Visual Studio에서-1 @no__t 가상 폴더 아래에 파일을 표시 하는 `Link` 요소가 추가 됩니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| url | 원격 protobuf 파일에 대 한 URL입니다. |

#### <a name="options"></a>변수

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -o | --출력 | 원격 protobuf 파일의 다운로드 경로를 지정 합니다. 필수 옵션입니다.
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.
| -S | --서비스 | 생성 해야 하는 gRPC 서비스의 유형입니다. @No__t-0이 지정 된 경우 웹 프로젝트에 `Both`이 사용 되 고 웹이 아닌 프로젝트에는 `Client`가 사용 됩니다. 허용 되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.
| -i | --추가 가져오기-디렉터리 | Protobuf 파일에 대 한 가져오기를 확인할 때 사용할 추가 디렉터리입니다. 세미콜론으로 구분 된 경로 목록입니다.
| | --액세스 | 생성 C# 된 클래스에 사용할 액세스 한정자입니다. 기본값은 `Public`여야 합니다. 허용 되는 값은 0 @no__t `Public`입니다.

## <a name="remove"></a>제거

@No__t-0 명령은 *.csproj* 파일에서 Protobuf 참조를 제거 하는 데 사용 됩니다. 이 명령은 경로 인수와 소스 Url을 인수로 받아들입니다. 도구:

* Protobuf 참조만 제거 합니다.
* 는 원래 원격 URL에서 다운로드 된 경우에도 해당 파일을 삭제 하지 않습니다 *.*

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| 참조 | 제거할 protobuf 참조의 Url 또는 파일 경로입니다. |

### <a name="options"></a>변수

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.

## <a name="refresh"></a>새로 고침

@No__t-0 명령은 원본 URL에서 최신 콘텐츠로 원격 참조를 업데이트 하는 데 사용 됩니다. 다운로드 파일 경로와 원본 URL을 모두 사용 하 여 업데이트할 참조를 지정할 수 있습니다. 참고:

* 파일 내용의 해시를 비교 하 여 로컬 파일을 업데이트 해야 하는지 여부를 확인 합니다.
* 타임 스탬프 정보는 비교 되지 않습니다.

업데이트를 필요로 하는 경우 도구는 항상 로컬 파일을 원격 파일로 바꿉니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| 참조 | 업데이트 해야 하는 원격 protobuf 참조에 대 한 Url 또는 파일 경로입니다. 모든 원격 참조를 새로 고치려면이 인수를 비워 둡니다. |

### <a name="options"></a>변수

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.
| | --dry-run | 새 콘텐츠를 다운로드 하지 않고 업데이트 되는 파일의 목록을 출력 합니다.

## <a name="list"></a>List

@No__t-0 명령은 프로젝트 파일에 모든 Protobuf 참조를 표시 하는 데 사용 됩니다. 열의 모든 값이 기본값이 면 열을 생략할 수 있습니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>변수

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
