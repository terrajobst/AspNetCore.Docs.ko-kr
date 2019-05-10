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
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서

ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio에서 프로젝트 템플릿에 포함 됩니다.

인증 템플릿을 사용 하 여.NET Core CLI에서 사용할 수 있습니다 `-au Individual`:

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

참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5833) web API 인증에 대 한 합니다.

<a name="no"></a>

## <a name="no-authentication"></a>인증 안 함

인증을 사용 하 여.NET Core CLI에 지정 된 `-au` 옵션입니다. Visual Studio에는 **인증 변경** 대화 상자는 새 웹 응용 프로그램에 사용할 수 있습니다. Visual Studio에서 새 웹 앱에 대 한 기본값은 **인증 없음**합니다.

인증 안 함을 사용 하 여 만든 프로젝트:

* 웹 페이지 및 UI에 로그인 및 로그 아웃에 없습니다.
* 인증 코드를 포함 하지 마십시오.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 인증

Windows 인증을 사용 하 여.NET Core CLI에서 새 웹 앱에 대 한 지정 된 `-au Windows` 옵션입니다. Visual Studio에서의 **인증 변경** 대화 상자를 사용 합니다 **Windows 인증** 옵션입니다.

앱을 사용 하도록 구성 된 Windows 인증을 선택 합니다 [Windows Authentication IIS 모듈](xref:host-and-deploy/iis/modules)합니다. Windows 인증 인트라넷 웹 사이트를 위한 것입니다.

## <a name="additional-resources"></a>추가 자료

다음 문서에는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.

* [SMS를 이용한 2단계 인증](xref:security/authentication/2fa)
* [ASP.NET Core의 계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data)
