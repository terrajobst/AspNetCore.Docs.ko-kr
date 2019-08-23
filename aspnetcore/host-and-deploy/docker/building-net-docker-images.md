---
title: ASP.NET Core의 Docker 이미지
author: tdykstra
description: Docker 레지스트리에서 게시된 .NET Core Docker 이미지를 사용하는 방법을 알아봅니다. 이미지를 끌어오고 고유한 이미지를 빌드합니다.
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 578f6f8cd54597fe0a6186d182cccc3955331e49
ms.sourcegitcommit: 2fa0ffe82a47c7317efc9ea908365881cbcb8ed7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2019
ms.locfileid: "69572857"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="e2609-104">ASP.NET Core의 Docker 이미지</span><span class="sxs-lookup"><span data-stu-id="e2609-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="e2609-105">이 자습서에서는 Docker 컨테이너에서 ASP.NET Core 앱을 실행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="e2609-106">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="e2609-107">Microsoft .NET Core Docker 이미지에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="e2609-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="e2609-108">ASP.NET Core 샘플 앱 다운로드</span><span class="sxs-lookup"><span data-stu-id="e2609-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="e2609-109">샘플 앱을 로컬로 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-109">Run the sample app locally</span></span>
> * <span data-ttu-id="e2609-110">Linux 컨테이너에서 샘플 앱 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="e2609-111">Windows 컨테이너에서 샘플 앱 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="e2609-112">수동으로 빌드 및 배포</span><span class="sxs-lookup"><span data-stu-id="e2609-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="e2609-113">ASP.NET Core Docker 이미지</span><span class="sxs-lookup"><span data-stu-id="e2609-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="e2609-114">이 자습서의 경우 ASP.NET Core 샘플 앱을 다운로드하고 Docker 컨테이너에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="e2609-115">샘플은 Linux 컨테이너와 Windows 컨테이너 둘 다에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="e2609-116">샘플 Dockerfile은 [Docker 다단계 빌드 기능](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)을 사용하여 서로 다른 컨테이너에서 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="e2609-117">빌드 및 실행 컨테이너는 Microsoft에서 Docker 허브에 제공한 이미지를 기반으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="e2609-118">샘플에서는 앱을 빌드하는 데 이 이미지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="e2609-119">이미지는 CLI(명령줄 도구)가 포함된 .NET Core SDK를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="e2609-120">이미지는 로컬 개발, 디버깅 및 유닛 테스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="e2609-121">개발 및 컴파일용으로 설치된 도구는 이 이미지를 비교적 큰 이미지로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="e2609-122">샘플에서는 앱을 실행하는 데 이 이미지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="e2609-123">이 이미지는 ASP.NET Core 런타임 및 라이브러리를 포함하며 프로덕션에서 실행 중인 앱에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="e2609-124">배포 및 앱 시작 속도를 위해 디자인된 이 이미지는 비교적 작기 때문에 Docker 레지스트리에서 Docker 호스트까지 네트워크 성능이 최적화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="e2609-125">앱을 실행하는 데 필요한 이진 파일 및 콘텐츠만 컨테이너에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="e2609-126">콘텐츠를 실행할 준비가 되면 `Docker run`부터 앱 시작까지 가장 빠른 시간에 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="e2609-127">동적 코드 컴파일은 Docker 모델에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2609-128">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e2609-128">Prerequisites</span></span>

* [<span data-ttu-id="e2609-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="e2609-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="e2609-130">Docker 클라이언트 18.03 이상</span><span class="sxs-lookup"><span data-stu-id="e2609-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="e2609-131">Linux 배포</span><span class="sxs-lookup"><span data-stu-id="e2609-131">Linux distributions</span></span>
    * [<span data-ttu-id="e2609-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="e2609-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="e2609-133">Debian</span><span class="sxs-lookup"><span data-stu-id="e2609-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="e2609-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="e2609-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="e2609-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="e2609-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="e2609-136">macOS</span><span class="sxs-lookup"><span data-stu-id="e2609-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="e2609-137">Windows</span><span class="sxs-lookup"><span data-stu-id="e2609-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="e2609-138">Git</span><span class="sxs-lookup"><span data-stu-id="e2609-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="e2609-139">샘플 앱 다운로드</span><span class="sxs-lookup"><span data-stu-id="e2609-139">Download the sample app</span></span>

* <span data-ttu-id="e2609-140">[.NET Core Docker 리포지토리](https://github.com/dotnet/dotnet-docker)를 복제하여 샘플을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="e2609-141">로컬에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-141">Run the app locally</span></span>

* <span data-ttu-id="e2609-142">*dotnet-docker/samples/aspnetapp/aspnetapp*에 있는 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="e2609-143">다음 명령을 실행하여 앱을 로컬로 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="e2609-144">브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="e2609-145">명령 프롬프트에서 Ctrl+C를 눌러 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="e2609-146">Linux 컨테이너에서 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-146">Run in a Linux container</span></span>

* <span data-ttu-id="e2609-147">Docker 클라이언트에서 Linux 컨테이너로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="e2609-148">*dotnet-docker/samples/aspnetapp*에 있는 Dockerfile 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="e2609-149">다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="e2609-150">`build` 명령 인수:</span><span class="sxs-lookup"><span data-stu-id="e2609-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="e2609-151">이미지 이름을 aspnetapp으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="e2609-152">현재 폴더(끝에 마침표)에서 Dockerfile을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="e2609-153">실행 명령 인수:</span><span class="sxs-lookup"><span data-stu-id="e2609-153">The run command arguments:</span></span>
  * <span data-ttu-id="e2609-154">의사 TTY를 할당하고 연결되지 않은 경우에도 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="e2609-155">(`--interactive --tty`와 결과가 동일함)</span><span class="sxs-lookup"><span data-stu-id="e2609-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="e2609-156">종료될 때 자동으로 컨테이너를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="e2609-157">로컬 머신의 포트 5000을 컨테이너의 포트 80에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="e2609-158">컨테이너 이름을 aspnetcore_sample로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="e2609-159">aspnetapp 이미지를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="e2609-160">브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="e2609-161">Windows 컨테이너에서 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-161">Run in a Windows container</span></span>

* <span data-ttu-id="e2609-162">Docker 클라이언트에서 Windows 컨테이너로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="e2609-163">`dotnet-docker/samples/aspnetapp`에 있는 Docker 파일 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="e2609-164">다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="e2609-165">Windows 컨테이너의 경우 컨테이너의 IP 주소가 필요합니다(`http://localhost:5000`로 이동되지 않음).</span><span class="sxs-lookup"><span data-stu-id="e2609-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="e2609-166">다른 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="e2609-167">`docker ps`를 실행하여 실행 중인 컨테이너를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="e2609-168">“aspnetcore_sample” 컨테이너가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="e2609-169">`docker exec aspnetcore_sample ipconfig`를 실행하여 컨테이너의 IP 주소를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="e2609-170">명령의 출력은 다음 예제와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="e2609-171">컨테이너 IPv4 주소(예: 172.29.245.43)를 복사하고 브라우저 주소 표시줄에 붙여넣어 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="e2609-172">수동으로 빌드 및 배포</span><span class="sxs-lookup"><span data-stu-id="e2609-172">Build and deploy manually</span></span>

<span data-ttu-id="e2609-173">일부 시나리오에서는 런타임에 필요한 애플리케이션 파일을 컨테이너에 복사하여 앱을 컨테이너에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="e2609-174">이 섹션에서는 수동으로 배포하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="e2609-175">*dotnet-docker/samples/aspnetapp/aspnetapp*에 있는 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="e2609-176">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-176">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="e2609-177">명령 인수:</span><span class="sxs-lookup"><span data-stu-id="e2609-177">The command arguments:</span></span>
  * <span data-ttu-id="e2609-178">릴리스 모드에서 애플리케이션을 빌드합니다(기본값은 디버그 모드).</span><span class="sxs-lookup"><span data-stu-id="e2609-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="e2609-179">*published* 폴더에서 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="e2609-180">애플리케이션을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-180">Run the application.</span></span>

  * <span data-ttu-id="e2609-181">Windows:</span><span class="sxs-lookup"><span data-stu-id="e2609-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="e2609-182">Linux:</span><span class="sxs-lookup"><span data-stu-id="e2609-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="e2609-183">`http://localhost:5000`으로 이동하여 홈페이지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-183">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="e2609-184">Docker 컨테이너 내에서 수동으로 게시된 애플리케이션을 사용하려면 새 Dockerfile을 만들고 `docker build .` 명령을 사용하여 컨테이너를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-184">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="e2609-185">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="e2609-185">The Dockerfile</span></span>

<span data-ttu-id="e2609-186">이전에 실행한 `docker build` 명령에서 사용되는 Dockerfile은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-186">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="e2609-187">이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-187">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="e2609-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2609-188">Additional resources</span></span>

* [<span data-ttu-id="e2609-189">Docker 빌드 명령</span><span class="sxs-lookup"><span data-stu-id="e2609-189">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="e2609-190">Docker 실행 명령</span><span class="sxs-lookup"><span data-stu-id="e2609-190">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="e2609-191">[ASP.NET Core Docker 샘플](https://github.com/dotnet/dotnet-docker)(이 자습서에서 사용되는 샘플)</span><span class="sxs-lookup"><span data-stu-id="e2609-191">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="e2609-192">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="e2609-192">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="e2609-193">Visual Studio Docker 도구로 작업</span><span class="sxs-lookup"><span data-stu-id="e2609-193">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* <span data-ttu-id="e2609-194">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="e2609-194">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)</span></span> 

## <a name="next-steps"></a><span data-ttu-id="e2609-195">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e2609-195">Next steps</span></span>

<span data-ttu-id="e2609-196">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-196">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="e2609-197">Microsoft .NET Core Docker 이미지에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="e2609-197">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="e2609-198">ASP.NET Core 샘플 앱 다운로드</span><span class="sxs-lookup"><span data-stu-id="e2609-198">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="e2609-199">샘플 앱을 로컬로 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-199">Run the sample app locally</span></span>
> * <span data-ttu-id="e2609-200">Linux 컨테이너에서 샘플 앱 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-200">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="e2609-201">Windows 컨테이너에서 샘플 실행</span><span class="sxs-lookup"><span data-stu-id="e2609-201">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="e2609-202">수동으로 빌드 및 배포</span><span class="sxs-lookup"><span data-stu-id="e2609-202">Built and deployed manually</span></span>

<span data-ttu-id="e2609-203">샘플 앱이 포함된 Git 리포지토리도 문서를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-203">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="e2609-204">리포지토리에서 사용 가능한 리소스의 개요를 보려면 [README 파일](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2609-204">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="e2609-205">특히, HTTPS를 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="e2609-205">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="e2609-206">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)(HTTPS를 통해 Docker를 사용하여 ASP.NET Core 애플리케이션 개발)</span><span class="sxs-lookup"><span data-stu-id="e2609-206">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)</span></span>
