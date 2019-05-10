---
title: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서
author: rick-anderson
description: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서를 검색 합니다.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892530"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="31871-103">개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서</span><span class="sxs-lookup"><span data-stu-id="31871-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="31871-104">ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio에서 프로젝트 템플릿에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31871-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="31871-105">인증 템플릿을 사용 하 여.NET Core CLI에서 사용할 수 있습니다 `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="31871-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="31871-106">참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5833) web API 인증에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="31871-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="31871-107">인증 안 함</span><span class="sxs-lookup"><span data-stu-id="31871-107">No Authentication</span></span>

<span data-ttu-id="31871-108">인증을 사용 하 여.NET Core CLI에 지정 된 `-au` 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="31871-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="31871-109">Visual Studio에는 **인증 변경** 대화 상자는 새 웹 응용 프로그램에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31871-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="31871-110">Visual Studio에서 새 웹 앱에 대 한 기본값은 **인증 없음**합니다.</span><span class="sxs-lookup"><span data-stu-id="31871-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="31871-111">인증 안 함을 사용 하 여 만든 프로젝트:</span><span class="sxs-lookup"><span data-stu-id="31871-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="31871-112">웹 페이지 및 UI에 로그인 및 로그 아웃에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="31871-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="31871-113">인증 코드를 포함 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="31871-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="31871-114">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="31871-114">Windows Authentication</span></span>

<span data-ttu-id="31871-115">Windows 인증을 사용 하 여.NET Core CLI에서 새 웹 앱에 대 한 지정 된 `-au Windows` 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="31871-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="31871-116">Visual Studio에서의 **인증 변경** 대화 상자를 사용 합니다 **Windows 인증** 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="31871-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="31871-117">앱을 사용 하도록 구성 된 Windows 인증을 선택 합니다 [Windows Authentication IIS 모듈](xref:host-and-deploy/iis/modules)합니다.</span><span class="sxs-lookup"><span data-stu-id="31871-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="31871-118">Windows 인증 인트라넷 웹 사이트를 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="31871-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="31871-119">추가 자료</span><span class="sxs-lookup"><span data-stu-id="31871-119">Additional resources</span></span>

<span data-ttu-id="31871-120">다음 문서에는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31871-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="31871-121">SMS를 이용한 2단계 인증</span><span class="sxs-lookup"><span data-stu-id="31871-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="31871-122">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="31871-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="31871-123">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="31871-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
