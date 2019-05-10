---
title: ASP.NET Core의 파일 공급자
author: guardrex
description: ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 93eb48d81a853061a874641e84b4875849690a93
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886618"
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core의 파일 공급자

작성자: [Steve Smith](https://ardalis.com/) 및 [Luke Latham](https://github.com/guardrex)

ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다. 파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.

* [IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment)는 앱의 콘텐츠 루트와 웹 루트를 `IFileProvider` 형식으로 노출합니다.
* [정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.
* [Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.
* .NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>파일 공급자 인터페이스

기본 인터페이스는 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)입니다. `IFileProvider`는 다음을 수행하는 메서드를 노출합니다.

* 파일 정보([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo))를 가져옵니다.
* 디렉터리 정보([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents))를 가져옵니다.
* 변경 알림([IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) 사용)을 설정합니다.

`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.

* [Exists](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [IsDirectory](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [이름](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* [Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length)(바이트)
* [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) 날짜

[IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) 메서드를 사용하여 파일에서 읽을 수 있습니다.

샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.

## <a name="file-provider-implementations"></a>파일 공급자 구현

`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.

| 구현 | 설명 |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | 물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 매니페스트 임베디드 공급자는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다. |
| [CompositeFileProvider](#compositefileprovider) | 마지막으로 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 복합적인 접근을 지원하기 위해서 사용됩니다. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider)는 물리적 파일 시스템에 대한 액세스 권한을 제공합니다. `PhysicalFileProvider`는 [System.IO.File](/dotnet/api/system.io.file) 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다. 이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다. 이 공급자를 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다. `PhysicalFileProvider` 공급자를 직접 인스턴스화하거나 생성자에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 `IFileProvider`를 요청할 수 있습니다.

**정적 형식**

다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

이전 예제의 형식:

* `provider`는 `IFileProvider`입니다.
* `contents`는 `IDirectoryContents`입니다.
* `fileInfo`는 `IFileInfo`입니다.

파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다. 파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.

샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

**종속성 주입을 사용하여 파일 공급자 형식 가져오기**

클래스 생성자에 공급자를 주입하고 이를 로컬 필드에 할당합니다. 클래스의 메서드 전체에 있는 필드를 사용하여 파일에 액세스합니다.

샘플 앱에서 `IndexModel` 클래스는 `IFileProvider` 인스턴스를 받아 앱의 기본 경로에 대한 디렉터리 콘텐츠를 가져옵니다.

*Pages/Index.cshtml.cs*:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

`IDirectoryContents`는 페이지에서 반복됩니다.

*Pages/Index.cshtml*:

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider)는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다. `ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.

포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다. [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.

샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

추가 오버로드를 사용하면 다음을 수행할 수 있습니다.

* 상대 파일 경로 지정
* 마지막으로 수정한 날짜로 파일의 범위 지정
* 포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정

| 오버로드 | 설명 |
| -------- | ----------- |
| [ManifestEmbeddedFileProvider(Assembly, String)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | 선택적인 `root` 상대 경로 매개 변수를 허용합니다. `root`를 지정하여 [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents)에 대한 호출의 범위를 제공된 경로 아래의 해당 리소스로 지정합니다. |
| [ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | 선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜([DateTimeOffset](/dotnet/api/system.datetimeoffset)) 매개 변수를 허용합니다. `lastModified` 날짜는 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)로 반환된 [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다. |
| [ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | 선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다. `manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다. |

### <a name="compositefileprovider"></a>CompositeFileProvider

[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider)는 `IFileProvider` 인스턴스를 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다. `CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.

샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>변경 내용 관찰

[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다. `Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다. `Watch`는 [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)을 반환합니다. 변경 토큰은 다음을 공개합니다.

* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.
* [RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다. 각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다. 모니터링을 지속적으로 수행하기 위해서는 다음 예제처럼 [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1)를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.

샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다. `DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).

## <a name="glob-patterns"></a>GLOB 패턴

파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다. 이러한 패턴을 사용하여 파일 그룹을 지정합니다. 두 개의 와일드카드 문자는 `*`와 `**`입니다.

**`*`**  
현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다. 파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.

**`**`**  
여러 디렉터리 수준에서 모든 것을 일치시킵니다. 디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.

**GLOB 패턴 예제**

**`directory/file.txt`**  
특정 디렉터리에 있는 특정 파일을 일치시킵니다.

**`directory/*.txt`**  
특정 디렉터리에서 확장명이 *.txt*인 파일을 모두 찾습니다.

**`directory/*/appsettings.json`**  
*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.

**`directory/**/*.txt`**  
*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt*인 파일을 모두 찾습니다.
