---
title: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서
author: rick-anderson
description: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서를 검색 합니다.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080697"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="e0343-103">개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서</span><span class="sxs-lookup"><span data-stu-id="e0343-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="e0343-104">ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio의 프로젝트 템플릿에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="e0343-105">인증 템플릿은 .NET Core CLI `-au Individual`에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="e0343-106">Web API 인증에 대 한 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5833) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e0343-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="e0343-107">인증 안 함</span><span class="sxs-lookup"><span data-stu-id="e0343-107">No Authentication</span></span>

<span data-ttu-id="e0343-108">인증은 `-au` 옵션을 사용 하 여 .NET Core CLI에 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="e0343-109">Visual Studio에서 새 웹 응용 프로그램에 대해 **인증 변경** 대화 상자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="e0343-110">Visual Studio의 새 웹 앱에 대 한 기본값은 **인증 안 함**입니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="e0343-111">인증 없이 만든 프로젝트:</span><span class="sxs-lookup"><span data-stu-id="e0343-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="e0343-112">로그인 및 로그 아웃 하는 웹 페이지와 UI가 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="e0343-113">인증 코드를 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="e0343-114">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="e0343-114">Windows Authentication</span></span>

<span data-ttu-id="e0343-115">.NET Core CLI에서 `-au Windows` 옵션을 사용 하 여 새 웹 앱에 대 한 Windows 인증을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="e0343-116">Visual Studio에서 **인증 변경** 대화 상자는 **Windows 인증** 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="e0343-117">Windows 인증을 선택 하면 앱이 [Windows 인증 IIS 모듈](xref:host-and-deploy/iis/modules)을 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="e0343-118">Windows 인증은 인트라넷 웹 사이트를 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0343-119">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e0343-119">Additional resources</span></span>

<span data-ttu-id="e0343-120">다음 문서에서는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e0343-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="e0343-121">SMS를 이용한 2단계 인증</span><span class="sxs-lookup"><span data-stu-id="e0343-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="e0343-122">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="e0343-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e0343-123">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e0343-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
