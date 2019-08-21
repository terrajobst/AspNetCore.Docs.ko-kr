---
page_type: sample
description: 이 자습서는 ASP.NET Core에서 gRPC 서비스 및 gRPC 클라이언트를 만드는 방법을 보여줍니다.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: a281adc3b1fe90eeb32c1185750f911af683af83
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029016"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="a0a2b-102">Visual Studio를 사용하여 ASP.NET Core 3.0에서 gRPC 클라이언트 및 서버 만들기</span><span class="sxs-lookup"><span data-stu-id="a0a2b-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="a0a2b-103">이 자습서에서는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트와 ASP.NET Core gRPC 서버를 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="a0a2b-104">끝으로 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="a0a2b-105">이 자습서에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-105">In this tutorial, you;</span></span>

* <span data-ttu-id="a0a2b-106">gRPC 서버를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="a0a2b-107">gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-107">Create a gRPC client.</span></span>
* <span data-ttu-id="a0a2b-108">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 서비스를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="a0a2b-109">gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="a0a2b-109">Create a gRPC service</span></span>

* <span data-ttu-id="a0a2b-110">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a0a2b-111">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a0a2b-112">**다음** 선택</span><span class="sxs-lookup"><span data-stu-id="a0a2b-112">Select **Next**</span></span>
* <span data-ttu-id="a0a2b-113">프로젝트 이름을 **GrpcGreeter**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="a0a2b-114">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="a0a2b-115">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-115">Select **Create**</span></span>
* <span data-ttu-id="a0a2b-116">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="a0a2b-117">드롭다운 메뉴에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="a0a2b-118">**gRPC 서비스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="a0a2b-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="a0a2b-120">서비스 실행</span><span class="sxs-lookup"><span data-stu-id="a0a2b-120">Run the service</span></span>

* <span data-ttu-id="a0a2b-121">`Ctrl+F5`를 눌러 디버거 없이 gRPC 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="a0a2b-122">Visual Studio는 명령 프롬프트에서 서비스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="a0a2b-123">로그에는 `https://localhost:5001`에서 수신하는 서비스가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-123">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> <span data-ttu-id="a0a2b-124">gRPC 템플릿은 [TLS(전송 계층 보안)](https://tools.ietf.org/html/rfc5246)를 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="a0a2b-125">gRPC 클라이언트는 HTTPS를 사용하여 서버를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="a0a2b-126">macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="a0a2b-127">macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="a0a2b-128">자세한 내용은 [macOS의 gRPC 및 ASP.NET Core](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="a0a2b-129">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="a0a2b-129">Examine the project files</span></span>

<span data-ttu-id="a0a2b-130">*GrpcGreeter* 프로젝트 파일:</span><span class="sxs-lookup"><span data-stu-id="a0a2b-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="a0a2b-131">*greet.proto*: *Protos/greet.proto* 파일은 `Greeter` gRPC를 정의하고 gRPC 서버 자산을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="a0a2b-132">자세한 내용은 [gRPC 소개](xref:grpc/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="a0a2b-133">*Services* 폴더: `Greeter` 서비스의 구현을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="a0a2b-134">*appSettings.json*: Kestrel에서 사용하는 프로토콜과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="a0a2b-135">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="a0a2b-136">*Program.cs*: gRPC 서비스의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="a0a2b-137">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="a0a2b-138">*Startup.cs*: 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="a0a2b-139">자세한 내용은 [앱 시작](xref:fundamentals/startup)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="a0a2b-140">.NET 콘솔 앱에서 gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="a0a2b-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="a0a2b-141">Visual Studio의 두 번째 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="a0a2b-142">메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="a0a2b-143">**새 프로젝트 만들기** 대화 상자에서 **콘솔 앱(.NET Core)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="a0a2b-144">**다음** 선택</span><span class="sxs-lookup"><span data-stu-id="a0a2b-144">Select **Next**</span></span>
* <span data-ttu-id="a0a2b-145">**이름** 텍스트 상자에 "GrpcGreeterClient"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="a0a2b-146">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="a0a2b-147">필수 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="a0a2b-147">Add required packages</span></span>

<span data-ttu-id="a0a2b-148">gRPC 클라이언트 프로젝트에는 다음 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="a0a2b-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)는 .NET Core 클라이언트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="a0a2b-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="a0a2b-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="a0a2b-152">도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="a0a2b-153">PMC(패키지 관리자 콘솔) 또는 NuGet 패키지 관리를 사용하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="a0a2b-154">패키지 설치를 위한 PMC 옵션</span><span class="sxs-lookup"><span data-stu-id="a0a2b-154">PMC option to install packages</span></span>

* <span data-ttu-id="a0a2b-155">Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="a0a2b-156">**패키지 관리자 콘솔** 창에서 *GrpcGreeterClient.csproj* 파일이 있는 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="a0a2b-157">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="a0a2b-158">패키지 설치를 위한 NuGet 패키지 관리 옵션</span><span class="sxs-lookup"><span data-stu-id="a0a2b-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="a0a2b-159">**솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="a0a2b-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="a0a2b-160">**찾아보기** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="a0a2b-161">검색 상자에 **Grpc.Net.Client**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="a0a2b-162">**찾아보기** 탭에서 **Grpc.Net.Client** 패키지를 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="a0a2b-163">`Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="a0a2b-164">greet.proto 추가</span><span class="sxs-lookup"><span data-stu-id="a0a2b-164">Add greet.proto</span></span>

* <span data-ttu-id="a0a2b-165">gRPC 클라이언트 프로젝트에서 **Protos** 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="a0a2b-166">**Protos\greet.proto** 파일을 gRPC Greeter 서비스에서 gRPC 클라이언트 프로젝트로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="a0a2b-167">*GrpcGreeterClient.csproj* 프로젝트 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="a0a2b-168">프로젝트를 마우스 오른쪽 단추로 클릭하고 **프로젝트 파일 편집**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="a0a2b-169">**greet.proto** 파일을 참조하는 `<Protobuf>` 요소를 사용하여 항목 그룹을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="a0a2b-170">Greeter 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="a0a2b-170">Create the Greeter client</span></span>

<span data-ttu-id="a0a2b-171">프로젝트를 빌드하여 `GrpcGreeter` 네임스페이스에 유형을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="a0a2b-172">`GrpcGreeter` 유형은 빌드 프로세스에 의해 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="a0a2b-173">gRPC 클라이언트 *Program.cs* 파일을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var httpClient = new HttpClient();
            // The port number(5001) must match the port of the gRPC server.
            httpClient.BaseAddress = new Uri("https://localhost:5001");
            var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="a0a2b-174">*Program.cs*는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="a0a2b-175">Greeter 클라이언트는 다음에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="a0a2b-176">gRPC 서비스에 대한 연결을 만들기 위한 정보가 포함된 `HttpClient` 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-176">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="a0a2b-177">`HttpClient`을 사용하여 Greeter 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-177">Using the `HttpClient` to construct the Greeter client:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

<span data-ttu-id="a0a2b-178">Greeter 클라이언트가 비동기 `SayHello` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-178">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="a0a2b-179">`SayHello` 호출의 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-179">The result of the `SayHello` call is displayed:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="a0a2b-180">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트</span><span class="sxs-lookup"><span data-stu-id="a0a2b-180">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="a0a2b-181">Greeter 서비스에서 `Ctrl+F5`를 눌러 디버거 없이 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-181">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="a0a2b-182">`GrpcGreeterClient` 프로젝트에서 `Ctrl+F5`를 눌러 디버거 없이 클라이언트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-182">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="a0a2b-183">클라이언트가 자신의 이름 “GreeterClient”가 포함된 메시지와 함께 인사말을 서비스에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-183">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="a0a2b-184">서비스는 "Hello GreeterClient"라는 메시지를 응답으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-184">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="a0a2b-185">"Hello GreeterClient" 응답이 명령 프롬프트에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-185">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="a0a2b-186">gRPC 서비스는 명령 프롬프트에 기록된 로그에 성공적인 호출의 세부 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="a0a2b-186">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="a0a2b-187">gRPC에 대한 문서 도움말 및 다음 단계</span><span class="sxs-lookup"><span data-stu-id="a0a2b-187">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="a0a2b-188">ASP.NET Core에서 gRPC 소개</span><span class="sxs-lookup"><span data-stu-id="a0a2b-188">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="a0a2b-189">C#을 사용하는 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="a0a2b-189">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="a0a2b-190">C Core에서 ASP.NET Core로 gRPC 서비스 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="a0a2b-190">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
