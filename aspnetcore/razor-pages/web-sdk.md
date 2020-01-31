---
title: ASP.NET Core 웹 SDK
author: Rick-Anderson
description: Microsoft .NET Sdk. 웹의 개요
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869768"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core 웹 SDK

### <a name="overview"></a>개요

`Microsoft.NET.Sdk.Web`은 ASP.NET Core 앱을 빌드하기 위한 [MSBuild 프로젝트 SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) 입니다. 이 SDK를 사용 하지 않고 ASP.NET Core 앱을 빌드할 수는 있지만 웹 SDK는 다음과 같습니다.

* 최고 수준의 환경 제공에 맞게 조정 되었습니다.
* 대부분의 사용자에 게 권장 되는 대상입니다.

프로젝트에서 웹 SDK를 사용 합니다.

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

웹 SDK를 사용 하 여 사용 하도록 설정 된 기능:

* .NET Core 3.0 이상을 대상으로 하는 프로젝트는 암시적으로 다음을 참조 합니다.

  * [ASP.NET Core 공유 프레임 워크](xref:fundamentals/metapackage-app)입니다.
  * ASP.NET Core 앱을 빌드하기 위해 설계 된 [분석기](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 입니다.
* 웹 SDK는 WebDeploy를 사용 하 여 게시 프로필 및 게시를 사용할 수 있도록 하는 MSBuild 대상을 가져옵니다.

### <a name="properties"></a>속성

| 속성 | 설명 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | `Microsoft.AspNetCore.App` 공유 프레임 워크에 대 한 암시적 참조를 사용 하지 않도록 설정 합니다. |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core 분석기에 대 한 암시적 참조를 사용 하지 않도록 설정 합니다. |
| `DisableImplicitComponentsAnalyzers` | Blazor (서버) 응용 프로그램을 빌드할 때 Razor 구성 요소 분석기에 대 한 암시적 참조를 사용 하지 않습니다. |
