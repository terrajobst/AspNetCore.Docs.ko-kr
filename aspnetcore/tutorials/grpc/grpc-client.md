---
title: '자습서: .NET Core gRPC 클라이언트 만들기'
author: juntaoluo
description: 이 자습서 시리즈는 ASP.NET Core에서 gRPC 서비스를 만드는 방법을 보여 줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: 031afbfaf097c518a85400b0b6abbc135c1bc611
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59674163"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="c4474-104">자습서: .NET Core gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="c4474-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="c4474-105">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="c4474-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="c4474-106">이 자습서는 gRPC 서비스와 통신할 수 있는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트를 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="c4474-107">끝으로 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="c4474-108">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4474-109">gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="c4474-110">이전 자습서에서 만든 gRPC Greeter 서비스에 대해 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="c4474-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="c4474-112">.NET 콘솔 애플리케이션 만들기</span><span class="sxs-lookup"><span data-stu-id="c4474-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c4474-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4474-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4474-114">[여기](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-114">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c4474-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4474-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4474-116">[여기](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-116">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c4474-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c4474-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c4474-118">[여기](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-on-mac-vs-full-solution)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-118">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="c4474-119">필수 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="c4474-119">Add required packages</span></span>

<span data-ttu-id="c4474-120">gRPC 클라이언트 프로젝트에 다음 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="c4474-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core)는 C 코어 클라이언트용 C# API를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="c4474-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="c4474-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="c4474-124">도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="c4474-125">다음 방법으로 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c4474-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4474-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="c4474-127">패키지 설치를 위한 PMC 옵션</span><span class="sxs-lookup"><span data-stu-id="c4474-127">PMC option to install packages</span></span>

* <span data-ttu-id="c4474-128">Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="c4474-129">**패키지 관리자 콘솔** 창에서 *GrpcGreeterClient.csproj* 파일이 있는 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="c4474-130">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="c4474-131">Google.Protobuf 및 Grpc.Tools에 대해 `Install-Package` 반복</span><span class="sxs-lookup"><span data-stu-id="c4474-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="c4474-132">패키지 설치를 위한 NuGet 패키지 관리 옵션</span><span class="sxs-lookup"><span data-stu-id="c4474-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="c4474-133">**솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="c4474-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="c4474-134">**패키지 소스**를 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="c4474-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="c4474-135">검색 상자에 "Grpc.Core" 입력</span><span class="sxs-lookup"><span data-stu-id="c4474-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="c4474-136">**찾아보기** 탭에서 "Grpc.Core" 패키지를 선택하고 **설치** 클릭</span><span class="sxs-lookup"><span data-stu-id="c4474-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="c4474-137">Google.Protobuf 및 Grpc.Tools에 대해 반복</span><span class="sxs-lookup"><span data-stu-id="c4474-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c4474-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4474-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4474-139">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Grpc.Core
```

<span data-ttu-id="c4474-140">Google.Protobuf 및 Grpc.Tools에 대해 반복</span><span class="sxs-lookup"><span data-stu-id="c4474-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c4474-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c4474-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4474-142">**Solution Pad**에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택</span><span class="sxs-lookup"><span data-stu-id="c4474-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="c4474-143">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="c4474-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="c4474-144">검색 상자에 "Grpc.Core" 입력</span><span class="sxs-lookup"><span data-stu-id="c4474-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="c4474-145">결과 창에서 "Grpc.Core" 패키지를 선택하고 **패키지 추가** 클릭</span><span class="sxs-lookup"><span data-stu-id="c4474-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="c4474-146">Google.Protobuf 및 Grpc.Tools에 대해 반복</span><span class="sxs-lookup"><span data-stu-id="c4474-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="c4474-147">greet.proto 파일 추가</span><span class="sxs-lookup"><span data-stu-id="c4474-147">Add the greet.proto file</span></span>

<span data-ttu-id="c4474-148">**Protos\greet.proto** 파일을 gRPC Greeter 서비스에서 gRPC 클라이언트 프로젝트로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="c4474-149">**greet.proto** 파일을 GrpcGreeterClient 프로젝트 파일의 `<Protobuf>` 항목 그룹에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="c4474-150">프로젝트를 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **GrpcGreeterClient.csproj 편집** 옵션을 선택하여 GrpcGreeterClient의 프로젝트 파일을 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="c4474-152">또는 GrpcGreeterClient 디렉터리로 이동하여 즐겨 찾기 편집기로 `GrpcGreeterClient.csproj`를 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="c4474-153">포함된 protobuf 파일에 대해 C# 클라이언트 자산만 생성되도록 `GrpcServices="Client"` 특성이 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="c4474-154">클라이언트 프로젝트를 빌드하여 C# 클라이언트 자산 생성을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="c4474-155">GreeterClient를 만들고 SayHello 단항 호출을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="c4474-156">gRPC 클라이언트 프로젝트 `Program.cs`파일의 `Main` 메서드에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="c4474-157">필수 유형에 액세스하려면 다음 using 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="c4474-158">GreeterClient는 gRPC 서비스에 대한 연결을 만들고 이를 사용하여 `GreeterClient`를 구성하는 정보를 포함하는 `Channel`을 인스턴스화하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="c4474-159">GreeterClient에는 비동기적으로 호출할 수 있는 단항 호출 `SayHello`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="c4474-160">`SayHello` 호출의 결과는 표시할 수 있는 `reply`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="c4474-161">모든 리소스를 해제하는 작업이 완료되면 클라이언트에서 사용하는 `Channel`을 종료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="c4474-162">**Greeter** 네임스페이스의 형식을 확인하려면 먼저 프로젝트를 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="c4474-163">이러한 형식은 빌드 중에 자동으로 생성되며 빌드를 실행하기 전에는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="c4474-164">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트</span><span class="sxs-lookup"><span data-stu-id="c4474-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c4474-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4474-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4474-166">이전 자습서에서 만든 Greeter 서비스가 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="c4474-167">서비스가 실행되면 시작 프로젝트로 설정된 **GrpcGreeterClient** 프로젝트로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="c4474-168">Ctrl+F5를 눌러 디버거 없이 클라이언트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="c4474-169">클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="c4474-170">서비스가 명령 프롬프트에 표시되는 응답으로 “Hello GreeterClient” 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/client.png)

  <span data-ttu-id="c4474-172">서비스가 명령 프롬프트에 기록되는 로그에 성공한 호출의 세부 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c4474-174">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c4474-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4474-175">이전 자습서에서 만든 Greeter 서비스가 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="c4474-176">`dotnet run`을 사용하여 별도의 명령줄에서 클라이언트 프로젝트 GrpcGreeter.Client를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="c4474-177">클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="c4474-178">서비스가 명령 프롬프트에 표시되는 응답으로 “Hello GreeterClient” 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="c4474-179">서비스가 명령 프롬프트에 기록되는 로그에 성공한 호출의 세부 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="c4474-180">gRPC 프로젝트의 프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="c4474-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="c4474-181">gRPC 클라이언트 GrpcGreeterClient 파일:</span><span class="sxs-lookup"><span data-stu-id="c4474-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="c4474-182">*Program.cs*는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4474-183">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c4474-183">Additional resources</span></span>

<span data-ttu-id="c4474-184">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4474-185">gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="c4474-186">이전 자습서에서 만든 gRPC Greeter 서비스에 대해 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="c4474-187">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c4474-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c4474-188">이전: gRPC Greeter 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="c4474-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
