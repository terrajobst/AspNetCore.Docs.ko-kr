---
title: '자습서: ASP.NET Core에서 gRPC 시작'
author: juntaoluo
description: 이 자습서 시리즈는 ASP.NET Core에서 gRPC 서비스를 만드는 방법을 보여 줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672569"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="ff22d-104">자습서: ASP.NET Core에서 gRPC 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="ff22d-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="ff22d-105">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ff22d-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ff22d-106">이 자습서는 ASP.NET Core에서 gRPC 서비스를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="ff22d-107">이 자습서를 마치면 인사말을 에코하는 gRPC 서비스를 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="ff22d-108">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff22d-109">gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="ff22d-110">gRPC 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-110">Run the gRPC service.</span></span>
> * <span data-ttu-id="ff22d-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="ff22d-112">gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="ff22d-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ff22d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff22d-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff22d-114">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ff22d-115">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="ff22d-116">![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="ff22d-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="ff22d-117">프로젝트 이름을 **GrpcGreeter**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ff22d-118">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="ff22d-119">![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="ff22d-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="ff22d-120">드롭다운에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="ff22d-121">**gRPC 서비스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="ff22d-122">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-122">The following starter project is created:</span></span>

  ![솔루션 탐색기](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ff22d-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff22d-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ff22d-125">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ff22d-126">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ff22d-127">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ff22d-128">`dotnet new` 명령은 *GrpcGreeter* 폴더에 새 gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ff22d-129">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *GrpcGreeter* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ff22d-130">다음과 같은 대화 상자가 표시됩니다. **‘GrpcGreeter’에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="ff22d-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ff22d-131">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ff22d-132">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ff22d-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ff22d-133">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="ff22d-134">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ff22d-135">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="ff22d-135">Open the project</span></span>

<span data-ttu-id="ff22d-136">Visual Studio에서 **파일 > 열기**를 선택하고 *GrpcGreeter.sln* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="ff22d-137">서비스 실행</span><span class="sxs-lookup"><span data-stu-id="ff22d-137">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ff22d-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff22d-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff22d-139">Ctrl-F5를 눌러 디버거 없이 gRPC 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-139">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="ff22d-140">Visual Studio는 명령 프롬프트에서 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="ff22d-141">로그에는 서비스가 `http://localhost:50051`에서 수신을 시작했다고 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ff22d-143">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ff22d-143">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ff22d-144">`dotnet run`을 사용하여 명령줄에서 gRPC Greeter 프로젝트 GrpcGreeter를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-144">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span> <span data-ttu-id="ff22d-145">로그에는 서비스가 `http://localhost:50051`에서 수신을 시작했다고 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-145">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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
```

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="ff22d-146">다음 자습서에서는 Greeter 서비스를 테스트하는 데 필요한 gRPC 클라이언트를 빌드하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-146">The next tutorial will demonstrate how to build a gRPC client, which is required to test the Greeter service.</span></span>

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="ff22d-147">gRPC 프로젝트의 프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="ff22d-147">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="ff22d-148">GrpcGreeter 파일:</span><span class="sxs-lookup"><span data-stu-id="ff22d-148">GrpcGreeter files:</span></span>

* <span data-ttu-id="ff22d-149">greet.proto: *Protos/greet.proto* 파일은 `Greeter` gRPC를 정의하고 gRPC 서버 자산을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-149">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ff22d-150">자세한 내용은 <xref:grpc/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff22d-150">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="ff22d-151">*Services* 폴더: `Greeter` 서비스의 구현을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-151">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ff22d-152">*appSettings.json*: Kestrel에서 사용하는 프로토콜과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-152">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ff22d-153">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff22d-153">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ff22d-154">*Program.cs*: gRPC 서비스의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-154">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ff22d-155">자세한 내용은 <xref:fundamentals/host/web-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff22d-155">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="ff22d-156">Startup.cs: 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-156">Startup.cs: Contains code that configures app behavior.</span></span> <span data-ttu-id="ff22d-157">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff22d-157">For more information, see <xref:fundamentals/startup>.</span></span>

### <a name="test-the-service"></a><span data-ttu-id="ff22d-158">서비스 테스트</span><span class="sxs-lookup"><span data-stu-id="ff22d-158">Test the service</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff22d-159">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ff22d-159">Additional resources</span></span>

<span data-ttu-id="ff22d-160">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-160">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff22d-161">gRPC 서비스를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-161">Created a gRPC service.</span></span>
> * <span data-ttu-id="ff22d-162">gRPC 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-162">Ran the gRPC service.</span></span>
> * <span data-ttu-id="ff22d-163">프로젝트 파일을 검사했습니다.</span><span class="sxs-lookup"><span data-stu-id="ff22d-163">Examined the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ff22d-164">다음: .NET Core gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="ff22d-164">Next: Create a .NET Core gRPC client</span></span>](xref:tutorials/grpc/grpc-client)