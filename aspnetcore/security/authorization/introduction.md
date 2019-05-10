---
title: ASP.NET Core에서 권한 부여 소개
author: rick-anderson
description: 권한 부여 및 권한 부여 ASP.NET Core 앱에서 작동 하는 방법의 기본 사항을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896980"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core에서 권한 부여 소개

<a name="security-authorization-introduction"></a>

권한 부여 결정 하는 프로세스를 가리킵니다. 사용자가 작업을 수행할 수 있습니다. 예를 들어, 관리자 문서 라이브러리 만들기, 문서 추가, 문서 편집 및 삭제할 수 있습니다. 라이브러리를 사용 하 여 작업 관리자가 아닌 사용자는 문서를 읽을 권한이.

권한 부여 부분도 있고 독립 인증 됩니다. 그러나 권한 부여는 인증 메커니즘을 필요합니다. 인증은 사용자가 익명인 프로세스입니다. 인증 현재 사용자에 대 한 하나 이상의 id를 만들 수 있습니다.

## <a name="authorization-types"></a>인증 형식

ASP.NET Core 권한 부여 제공을 간단 하 고 선언적인 [역할](xref:security/authorization/roles) 와 다양 한 [정책 기반](xref:security/authorization/policies) 모델입니다. 권한 부여 요구 사항에서 표현 됩니다 및 요구 사항에 대 한 사용자의 클레임을 평가 하는 처리기. 명령적 검사는 간단한 정책 또는 사용자 id 및 사용자가 액세스 하려고 하는 리소스의 속성을 모두 평가 하는 정책을 기반으로 사용할 수 있습니다.

## <a name="namespaces"></a>네임스페이스

권한 부여 구성 요소를 포함 하는 `AuthorizeAttribute` 및 `AllowAnonymousAttribute` 특성에 포함 됩니다는 `Microsoft.AspNetCore.Authorization` 네임 스페이스입니다.

설명서를 참조 하세요 [단순 권한 부여](xref:security/authorization/simple)합니다.
