---
title: ASP.NET Core 시작
author: rick-anderson
description: ASP.NET Core를 사용하여 기본 Hello World 앱을 만들고 실행하는 간단한 자습서입니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081115"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="ce6a3-103">자습서: ASP.NET Core 시작</span><span class="sxs-lookup"><span data-stu-id="ce6a3-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="ce6a3-104">이 자습서에서는 .NET Core 명령줄 인터페이스를 사용하여 ASP.NET Core 웹앱을 만들고 실행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="ce6a3-105">다음을 수행하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ce6a3-106">웹앱 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-106">Create a web app project.</span></span>
> * <span data-ttu-id="ce6a3-107">개발 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="ce6a3-108">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-108">Run the app.</span></span>
> * <span data-ttu-id="ce6a3-109">Razor 페이지를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-109">Edit a Razor page.</span></span>

<span data-ttu-id="ce6a3-110">마지막에는 로컬 머신에서 작업 웹앱이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-110">At the end, you'll have a working web app running on your local machine.</span></span>

![웹앱 홈페이지](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="ce6a3-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="ce6a3-112">Prerequisites</span></span>

* [<span data-ttu-id="ce6a3-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="ce6a3-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="ce6a3-114">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="ce6a3-114">Create a web app project</span></span>

<span data-ttu-id="ce6a3-115">명령 셸을 열고 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-115">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="ce6a3-116">개발 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="ce6a3-116">Trust the development certificate</span></span>

<span data-ttu-id="ce6a3-117">HTTPS 개발 인증서 신뢰:</span><span class="sxs-lookup"><span data-stu-id="ce6a3-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="ce6a3-118">Windows</span><span class="sxs-lookup"><span data-stu-id="ce6a3-118">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="ce6a3-119">이전 명령으로 인해 다음 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-119">The preceding command displays the following dialog:</span></span>

![보안 경고 대화 상자](~/getting-started/_static/cert.png)

<span data-ttu-id="ce6a3-121">개발 인증서를 신뢰하는 데 동의하는 경우 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="ce6a3-122">macOS</span><span class="sxs-lookup"><span data-stu-id="ce6a3-122">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="ce6a3-123">이전 명령으로 인해 다음 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="ce6a3-124">*HTTPS 개발 인증서를 신뢰해야 합니다. 인증서를 신뢰할 수 없는 경우*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="ce6a3-125">이 명령은 시스템 키 집합에 인증서를 설치하기 위해 암호를 묻는 메시지를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="ce6a3-126">개발 인증서를 신뢰하는 데 동의하는 경우 암호를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="ce6a3-127">Linux</span><span class="sxs-lookup"><span data-stu-id="ce6a3-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="ce6a3-128">Linux용 Windows 하위 시스템의 경우 [Linux용 Windows 하위 시스템에서 HTTPS 인증서 신뢰](xref:security/enforcing-ssl#wsl)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="ce6a3-129">HTTPS 개발 인증서를 신뢰하는 방법은 Linux 배포에 대한 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="ce6a3-130">자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ce6a3-131">앱 실행</span><span class="sxs-lookup"><span data-stu-id="ce6a3-131">Run the app</span></span>

<span data-ttu-id="ce6a3-132">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-132">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="ce6a3-133">명령 셸에서 앱이 시작되었음을 나타낸 후 [https://localhost:5001](https://localhost:5001)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="ce6a3-134">**동의**를 클릭하여 개인정보처리방침 정책에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="ce6a3-135">이 앱은 개인 정보를 보관하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="ce6a3-136">Razor 페이지 편집</span><span class="sxs-lookup"><span data-stu-id="ce6a3-136">Edit a Razor page</span></span>

<span data-ttu-id="ce6a3-137">*Pages/Index.cshtml*을 열고 다음에서 강조 표시된 영역처럼 페이지를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="ce6a3-138">[https://localhost:5001](https://localhost:5001)로 이동하여 변경 내용이 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ce6a3-139">다음 단계</span><span class="sxs-lookup"><span data-stu-id="ce6a3-139">Next steps</span></span>

<span data-ttu-id="ce6a3-140">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ce6a3-141">웹앱 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-141">Create a web app project.</span></span>
> * <span data-ttu-id="ce6a3-142">개발 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="ce6a3-143">프로젝트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-143">Run the project.</span></span>
> * <span data-ttu-id="ce6a3-144">페이지를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-144">Make a change.</span></span>

<span data-ttu-id="ce6a3-145">ASP.NET Core에 대해 자세히 알아보려면 소개에서 권장되는 학습 경로를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ce6a3-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
