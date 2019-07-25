---
title: ASP.NET Core에서 .NET Core gRPC 클라이언트 및 서버 만들기
author: juntaoluo
description: 이 자습서는 ASP.NET Core에서 gRPC 서비스 및 gRPC 클라이언트를 만드는 방법을 보여줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 3e90e3b17186757fe157fb6641888786bb7a0df2
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412528"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="70f87-104">자습서: ASP.NET Core에서 gRPC 클라이언트 및 서버 만들기</span><span class="sxs-lookup"><span data-stu-id="70f87-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="70f87-105">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="70f87-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="70f87-106">이 자습서에서는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트와 ASP.NET Core gRPC 서버를 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="70f87-107">끝으로 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="70f87-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70f87-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="70f87-109">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="70f87-110">gRPC 서버를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="70f87-111">gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="70f87-112">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 서비스를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70f87-113">전제 조건</span><span class="sxs-lookup"><span data-stu-id="70f87-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70f87-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70f87-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70f87-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="70f87-117">gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="70f87-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70f87-119">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="70f87-120">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="70f87-121">**다음** 선택</span><span class="sxs-lookup"><span data-stu-id="70f87-121">Select **Next**</span></span>
* <span data-ttu-id="70f87-122">프로젝트 이름을 **GrpcGreeter**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="70f87-123">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="70f87-124">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-124">Select **Create**</span></span>
* <span data-ttu-id="70f87-125">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="70f87-126">드롭다운 메뉴에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="70f87-127">**gRPC 서비스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="70f87-128">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70f87-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70f87-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="70f87-130">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="70f87-131">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="70f87-132">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="70f87-133">`dotnet new` 명령은 *GrpcGreeter* 폴더에 새 gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="70f87-134">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *GrpcGreeter* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="70f87-135">다음과 같은 대화 상자가 표시됩니다. **‘GrpcGreeter’에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="70f87-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="70f87-136">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70f87-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="70f87-138">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="70f87-139">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="70f87-140">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="70f87-140">Open the project</span></span>

<span data-ttu-id="70f87-141">Visual Studio에서 **파일 > 열기**를 선택하고 *GrpcGreeter.sln* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="70f87-142">서비스 실행</span><span class="sxs-lookup"><span data-stu-id="70f87-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70f87-144">`Ctrl+F5`를 눌러 디버거 없이 gRPC 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="70f87-145">Visual Studio는 명령 프롬프트에서 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="70f87-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="70f87-147">`dotnet run`을 사용하여 명령줄에서 gRPC Greeter 프로젝트 *GrpcGreeter*를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="70f87-148">로그에는 `https://localhost:5001`에서 수신하는 서비스가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="70f87-149">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="70f87-149">Examine the project files</span></span>

<span data-ttu-id="70f87-150">*GrpcGreeter* 프로젝트 파일:</span><span class="sxs-lookup"><span data-stu-id="70f87-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="70f87-151">*greet.proto*: *Protos/greet.proto* 파일은 `Greeter` gRPC를 정의하고 gRPC 서버 자산을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="70f87-152">자세한 내용은 [gRPC 소개](xref:grpc/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="70f87-153">*Services* 폴더: `Greeter` 서비스의 구현을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="70f87-154">*appSettings.json*: Kestrel에서 사용하는 프로토콜과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="70f87-155">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="70f87-156">*Program.cs*: gRPC 서비스의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="70f87-157">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="70f87-158">*Startup.cs*: 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="70f87-159">자세한 내용은 [앱 시작](xref:fundamentals/startup)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="70f87-160">.NET 콘솔 앱에서 gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="70f87-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70f87-162">Visual Studio의 두 번째 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="70f87-163">메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="70f87-164">**새 프로젝트 만들기** 대화 상자에서 **콘솔 앱(.NET Core)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="70f87-165">**다음** 선택</span><span class="sxs-lookup"><span data-stu-id="70f87-165">Select **Next**</span></span>
* <span data-ttu-id="70f87-166">**이름** 텍스트 상자에 "GrpcGreeterClient"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="70f87-167">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70f87-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70f87-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="70f87-169">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="70f87-170">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="70f87-171">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70f87-172">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="70f87-173">[여기](/dotnet/core/tutorials/using-on-mac-vs-full-solution)의 지침에 따라 *GrpcGreeterClient*라는 이름으로 콘솔 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="70f87-174">필수 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="70f87-174">Add required packages</span></span>

<span data-ttu-id="70f87-175">gRPC 클라이언트 프로젝트에는 다음 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="70f87-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)는 .NET Core 클라이언트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="70f87-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="70f87-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="70f87-179">도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="70f87-181">PMC(패키지 관리자 콘솔) 또는 NuGet 패키지 관리를 사용하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="70f87-182">패키지 설치를 위한 PMC 옵션</span><span class="sxs-lookup"><span data-stu-id="70f87-182">PMC option to install packages</span></span>

* <span data-ttu-id="70f87-183">Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="70f87-184">**패키지 관리자 콘솔** 창에서 *GrpcGreeterClient.csproj* 파일이 있는 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="70f87-185">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="70f87-186">패키지 설치를 위한 NuGet 패키지 관리 옵션</span><span class="sxs-lookup"><span data-stu-id="70f87-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="70f87-187">**솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="70f87-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="70f87-188">**찾아보기** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="70f87-189">검색 상자에 **Grpc.Core**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="70f87-190">**찾아보기** 탭에서 **Grpc.Core** 패키지를 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="70f87-191">`Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70f87-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70f87-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="70f87-193">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70f87-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70f87-195">**Solution Pad** > **패키지 추가**에서 **패키지** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="70f87-196">검색 상자에 **Grpc.Net.Client**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="70f87-197">결과 창에서 **Grpc.Net.Client** 패키지를 선택하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="70f87-198">`Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.</span><span class="sxs-lookup"><span data-stu-id="70f87-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="70f87-199">greet.proto 추가</span><span class="sxs-lookup"><span data-stu-id="70f87-199">Add greet.proto</span></span>

* <span data-ttu-id="70f87-200">gRPC 클라이언트 프로젝트에서 **Protos** 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="70f87-201">**Protos\greet.proto** 파일을 gRPC Greeter 서비스에서 gRPC 클라이언트 프로젝트로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="70f87-202">*GrpcGreeterClient.csproj* 프로젝트 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="70f87-204">프로젝트를 마우스 오른쪽 단추로 클릭하고 **프로젝트 파일 편집**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70f87-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70f87-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="70f87-206">*GrpcGreeterClient.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70f87-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="70f87-208">프로젝트를 마우스 오른쪽 단추로 클릭하고 **도구 > 파일 편집**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="70f87-209">**greet.proto** 파일을 참조하는 `<Protobuf>` 요소를 사용하여 항목 그룹을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="70f87-210">Greeter 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="70f87-210">Create the Greeter client</span></span>

<span data-ttu-id="70f87-211">프로젝트를 빌드하여 `GrpcGreeter` 네임스페이스에 유형을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="70f87-212">`GrpcGreeter` 유형은 빌드 프로세스에 의해 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="70f87-213">gRPC 클라이언트 *Program.cs* 파일을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="70f87-214">*Program.cs*는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="70f87-215">Greeter 클라이언트는 다음에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="70f87-216">gRPC 서비스에 대한 연결을 만들기 위한 정보가 포함된 `HttpClient` 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="70f87-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="70f87-217">`HttpClient`을 사용하여 Greeter 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="70f87-218">Greeter 클라이언트가 비동기 `SayHello` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="70f87-219">`SayHello` 호출의 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="70f87-220">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트</span><span class="sxs-lookup"><span data-stu-id="70f87-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70f87-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f87-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70f87-222">Greeter 서비스에서 `Ctrl+F5`를 눌러 디버거 없이 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="70f87-223">`GrpcGreeterClient` 프로젝트에서 `Ctrl+F5`를 눌러 디버거 없이 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="70f87-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70f87-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="70f87-225">Greeter 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-225">Start the Greeter service.</span></span>
* <span data-ttu-id="70f87-226">클라이언트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="70f87-227">클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="70f87-228">서비스는 "Hello GreeterClient"라는 메시지를 응답으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="70f87-229">"Hello GreeterClient" 응답이 명령 프롬프트에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="70f87-230">gRPC 서비스는 명령 프롬프트에 기록된 로그에 성공적인 호출의 세부 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="70f87-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a><span data-ttu-id="70f87-231">다음 단계</span><span class="sxs-lookup"><span data-stu-id="70f87-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
