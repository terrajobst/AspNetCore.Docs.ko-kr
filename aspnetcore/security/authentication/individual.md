---
title: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서
author: rick-anderson
description: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서를 검색 합니다.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: 91c5665dc50124b3ba09bdcfbf3ba501f684c604
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463039"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서

ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio의 프로젝트 템플릿에 포함 됩니다.

인증 템플릿은 `-au Individual`와 함께 .NET Core CLI에서 사용할 수 있습니다.

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

Web API 인증에 대 한 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5833) 를 참조 하세요.

<a name="no"></a>

## <a name="no-authentication"></a>인증 안 함

인증은 `-au` 옵션을 사용 하 여 .NET Core CLI에 지정 됩니다. Visual Studio에서 새 웹 응용 프로그램에 대해 **인증 변경** 대화 상자를 사용할 수 있습니다. Visual Studio의 새 웹 앱에 대 한 기본값은 **인증 안 함**입니다.

인증 없이 만든 프로젝트:

* 로그인 및 로그 아웃 하는 웹 페이지와 UI가 포함 되지 않습니다.
* 인증 코드를 포함 하지 않습니다.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 인증

`-au Windows` 옵션을 사용 하 여 .NET Core CLI의 새 웹 앱에 대해 Windows 인증을 지정 합니다. Visual Studio에서 **인증 변경** 대화 상자는 **Windows 인증** 옵션을 제공 합니다.

Windows 인증을 선택 하면 앱이 [Windows 인증 IIS 모듈](xref:host-and-deploy/iis/modules)을 사용 하도록 구성 됩니다. Windows 인증은 인트라넷 웹 사이트를 위한 것입니다.

## <a name="additional-resources"></a>추가 자료

다음 문서에서는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.

* [ASP.NET Core의 계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data)
