---
title: SignalR에서 사용자 및 그룹 관리
author: bradygaster
description: 사용자 및 그룹 관리 SignalR ASP.NET Core 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 59e90042ecbaf936602643bbdc3965e036426b26
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963818"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>SignalR에서 사용자 및 그룹 관리

만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)

SignalR를 사용 하면 명명 된 연결 그룹 뿐만 아니라 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>SignalR의 사용자

SignalR를 사용 하 여 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다. 기본적으로 SignalR는 연결과 관련 된 `ClaimsPrincipal`의 `ClaimTypes.NameIdentifier`를 사용자 식별자로 사용 합니다. 단일 사용자는 SignalR 앱에 대 한 여러 연결을 가질 수 있습니다. 예를 들어 사용자는 자신의 데스크톱 및 휴대폰에 연결 될 수 있습니다. 각 장치에는 별도의 SignalR 연결이 있지만 모두 동일한 사용자와 연결 됩니다. 사용자에 게 메시지를 보내는 경우 해당 사용자와 연결 된 모든 연결에서 메시지를 받습니다. 허브의 `Context.UserIdentifier` 속성을 사용 하 여 연결에 대 한 사용자 id에 액세스할 수 있습니다.

다음 예제와 같이 허브 메서드에서 사용자 id를 `User` 함수에 전달 하 여 특정 사용자에 게 메시지를 보냅니다.

> [!NOTE]
> 사용자 id는 대/소문자를 구분 합니다.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>SignalR의 그룹

그룹은 이름과 연결 된 연결의 컬렉션입니다. 그룹의 모든 연결에 메시지를 보낼 수 있습니다. 그룹은 응용 프로그램에서 관리 되기 때문에 연결 또는 다중 연결에 전송 하는 데 권장 되는 방법입니다. 연결은 여러 그룹의 멤버일 수 있습니다. 이를 통해 그룹은 채팅 응용 프로그램과 같은 항목에 이상적으로 사용할 수 있습니다. 여기서 각 공간은 그룹으로 나타낼 수 있습니다. 연결은 `AddToGroupAsync` 및 `RemoveFromGroupAsync` 방법을 통해 그룹에 추가 하거나 그룹에서 제거할 수 있습니다.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

연결이 다시 연결 되 면 그룹 멤버 자격은 유지 되지 않습니다. 다시 설정 된 후에는 연결에서 그룹에 다시 조인 해야 합니다. 응용 프로그램을 여러 서버로 확장 하는 경우에는이 정보를 사용할 수 없으므로 그룹의 멤버 수를 계산할 수 없습니다.

그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 ASP.NET Core의 [인증 및 권한 부여](xref:signalr/authn-and-authz) 기능을 사용 합니다. 해당 그룹에 대 한 자격 증명이 유효한 경우에만 그룹에 사용자를 추가 하는 경우 해당 그룹에 전송 된 메시지는 권한 있는 사용자로만 이동 합니다. 그러나 그룹은 보안 기능이 아닙니다. 인증 클레임에는 만료 및 해지와 같이 그룹에서 제공 하지 않는 기능이 있습니다. 사용자의 그룹 액세스 권한이 해지 된 경우 해당 사용자를 수동으로 검색 하 여 그룹에서 제거 해야 합니다.

> [!NOTE]
> 그룹 이름은 대/소문자를 구분 합니다.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
