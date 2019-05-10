---
title: '자습서: .NET Core gRPC 클라이언트 만들기'
author: juntaoluo
description: 이 자습서 시리즈는 ASP.NET Core에서 gRPC 서비스를 만드는 방법을 보여 줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212644"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a>자습서: .NET Core gRPC 클라이언트 만들기

작성자: [John Luo](https://github.com/juntaoluo)

이 자습서는 gRPC 서비스와 통신할 수 있는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트를 만드는 방법을 보여줍니다.

끝으로 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * gRPC 클라이언트를 만듭니다.
> * 이전 자습서에서 만든 gRPC Greeter 서비스에 대해 서비스를 실행합니다.
> * 프로젝트 파일을 검사합니다.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a>.NET 콘솔 애플리케이션 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[여기](/dotnet/core/tutorials/with-visual-studio)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[여기](/dotnet/core/tutorials/with-visual-studio-code)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[여기](/dotnet/core/tutorials/using-on-mac-vs-full-solution)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a>필수 패키지 추가

gRPC 클라이언트 프로젝트에 다음 패키지를 추가합니다.

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core)는 C 코어 클라이언트용 C# API를 포함합니다.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다. 도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.

다음 방법으로 패키지를 추가할 수 있습니다.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a>패키지 설치를 위한 PMC 옵션

* Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
* **패키지 관리자 콘솔** 창에서 *GrpcGreeterClient.csproj* 파일이 있는 디렉터리로 이동합니다.
* 다음 명령을 실행합니다.

    ```powershell
    Install-Package Grpc.Core
    ```

* Google.Protobuf 및 Grpc.Tools에 대해 `Install-Package` 반복

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a>패키지 설치를 위한 NuGet 패키지 관리 옵션

* **솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭
* **패키지 소스**를 “nuget.org”로 설정
* 검색 상자에 "Grpc.Core" 입력
* **찾아보기** 탭에서 "Grpc.Core" 패키지를 선택하고 **설치** 클릭
* Google.Protobuf 및 Grpc.Tools에 대해 반복

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널**에서 다음 명령을 실행합니다.

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

Google.Protobuf 및 Grpc.Tools에 대해 반복

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **Solution Pad**에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택
* **패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정
* 검색 상자에 "Grpc.Core" 입력
* 결과 창에서 "Grpc.Core" 패키지를 선택하고 **패키지 추가** 클릭
* Google.Protobuf 및 Grpc.Tools에 대해 반복

---

## <a name="add-the-greetproto-file"></a>greet.proto 파일 추가

**Protos\greet.proto** 파일을 gRPC Greeter 서비스에서 gRPC 클라이언트 프로젝트로 복사합니다. **greet.proto** 파일을 GrpcGreeterClient 프로젝트 파일의 `<Protobuf>` 항목 그룹에 추가합니다.

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> 프로젝트를 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **GrpcGreeterClient.csproj 편집** 옵션을 선택하여 GrpcGreeterClient의 프로젝트 파일을 열 수 있습니다.
>
> ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/edit_csproj.png)
>
> 또는 GrpcGreeterClient 디렉터리로 이동하여 즐겨 찾기 편집기로 `GrpcGreeterClient.csproj`를 편집할 수 있습니다.

포함된 protobuf 파일에 대해 C# 클라이언트 자산만 생성되도록 `GrpcServices="Client"` 특성이 추가되었습니다. 클라이언트 프로젝트를 빌드하여 C# 클라이언트 자산 생성을 트리거합니다.

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a>GreeterClient를 만들고 SayHello 단항 호출을 호출합니다.

gRPC 클라이언트 프로젝트 `Program.cs`파일의 `Main` 메서드에 다음 코드를 추가합니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

필수 유형에 액세스하려면 다음 using 문이 필요합니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

GreeterClient는 gRPC 서비스에 대한 연결을 만들고 이를 사용하여 `GreeterClient`를 구성하는 정보를 포함하는 `Channel`을 인스턴스화하여 생성됩니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

GreeterClient에는 비동기적으로 호출할 수 있는 단항 호출 `SayHello`가 포함되어 있습니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

`SayHello` 호출의 결과는 표시할 수 있는 `reply`에 저장됩니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

모든 리소스를 해제하는 작업이 완료되면 클라이언트에서 사용하는 `Channel`을 종료해야 합니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> **Greeter** 네임스페이스의 형식을 확인하려면 먼저 프로젝트를 빌드해야 합니다. 이러한 형식은 빌드 중에 자동으로 생성되며 빌드를 실행하기 전에는 사용할 수 없습니다.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 이전 자습서에서 만든 Greeter 서비스가 실행 중인지 확인합니다.

* 서비스가 실행되면 시작 프로젝트로 설정된 **GrpcGreeterClient** 프로젝트로 돌아갑니다. Ctrl+F5를 눌러 디버거 없이 클라이언트를 실행합니다.

  클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다. 서비스가 명령 프롬프트에 표시되는 응답으로 “Hello GreeterClient” 메시지를 보냅니다.

  ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/client.png)

  서비스가 명령 프롬프트에 기록되는 로그에 성공한 호출의 세부 정보를 기록합니다.

  ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 이전 자습서에서 만든 Greeter 서비스가 실행 중인지 확인합니다.

* `dotnet run`을 사용하여 별도의 명령줄에서 클라이언트 프로젝트 GrpcGreeter.Client를 실행합니다.

클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다. 서비스가 명령 프롬프트에 표시되는 응답으로 “Hello GreeterClient” 메시지를 보냅니다.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

서비스가 명령 프롬프트에 기록되는 로그에 성공한 호출의 세부 정보를 기록합니다.

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>gRPC 프로젝트의 프로젝트 파일 검토

gRPC 클라이언트 GrpcGreeterClient 파일:

*Program.cs*는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.

## <a name="additional-resources"></a>추가 자료

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * gRPC 클라이언트를 만듭니다.
> * 이전 자습서에서 만든 gRPC Greeter 서비스에 대해 서비스를 실행합니다.
> * 프로젝트 파일을 검사합니다.

> [!div class="step-by-step"]
> [이전: gRPC Greeter 서비스 만들기](xref:tutorials/grpc/grpc-start)
