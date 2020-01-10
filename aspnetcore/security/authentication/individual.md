---
title: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서
author: rick-anderson
description: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서를 검색 합니다.
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828713"
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

Web API 인증에 대 한 [이 GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5833) 를 참조 하세요.

<a name="no"></a>

## <a name="no-authentication"></a>인증 없음

인증은 `-au` 옵션을 사용 하 여 .NET Core CLI에 지정 됩니다. Visual Studio에서 새 웹 응용 프로그램에 대해 **인증 변경** 대화 상자를 사용할 수 있습니다. Visual Studio의 새 웹 앱에 대 한 기본값은 **인증 안 함**입니다.

인증 없이 만든 프로젝트:

* 로그인 및 로그 아웃 하는 웹 페이지와 UI가 포함 되지 않습니다.
* 인증 코드를 포함 하지 않습니다.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 인증

`-au Windows` 옵션을 사용 하 여 .NET Core CLI의 새 웹 앱에 대해 Windows 인증을 지정 합니다. Visual Studio에서 **인증 변경** 대화 상자는 **Windows 인증** 옵션을 제공 합니다.

Windows 인증을 선택 하면 앱이 [Windows 인증 IIS 모듈](xref:host-and-deploy/iis/modules)을 사용 하도록 구성 됩니다. Windows 인증은 인트라넷 웹 사이트를 위한 것입니다.

## <a name="dotnet-new-webapp-authentication-options"></a>dotnet new webapp 인증 옵션

다음 표에서는 새 웹 앱에 사용할 수 있는 인증 옵션을 보여 줍니다.

| 옵션 | 인증 유형 | 자세한 정보에 대한 링크 |
 | ----------------- | ------------ | ---------- |
| None            |  인증 없음 | | 
| 개인      |  개별 인증 | <xref:security/authentication/identity>
| IndividualB2C   |  Azure AD B2C를 사용 하 여 클라우드 호스팅 개별 인증 | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  단일 테 넌 트에 대 한 조직 인증 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  여러 테 넌 트에 대 한 조직 인증 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 인증 | [Windows 인증](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio new webapp 인증 옵션

다음 표에서는 Visual Studio를 사용 하 여 새 웹 앱을 만들 때 사용할 수 있는 인증 옵션을 보여 줍니다.

| 옵션 | 인증 유형 | 자세한 정보에 대한 링크 |
 | ----------------- | ------------ | ---------- |
| None            |  인증 없음 | | 
| 개별 사용자 계정/앱 내 사용자 계정 저장 |  개별 인증 | <xref:security/authentication/identity> |
| 개별 사용자 계정/클라우드의 기존 사용자 저장소에 연결 |  Azure AD B2C를 사용 하 여 클라우드 호스팅 개별 인증 | [Azure AD B2C](/azure/active-directory-b2c/) |
| 회사 또는 학교 클라우드/단일 조직  |  단일 테 넌 트에 대 한 조직 인증 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| 회사 또는 학교 클라우드/여러 조직 |  여러 테 넌 트에 대 한 조직 인증 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 인증 | [Windows 인증](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>추가 자료

다음 문서에서는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.

* [ASP.NET Core의 계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data)
