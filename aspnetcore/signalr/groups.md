---
title: SignalR에서 사용자 및 그룹 관리
author: bradygaster
description: ASP.NET Core SignalR의 사용자 및 그룹 관리에 대한 개요입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 180f8b4551eea39cc340bf1d250f4575cb5f71ed
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087425"
---
# <a name="manage-users-and-groups-in-signalr"></a>SignalR에서 사용자 및 그룹 관리

작성자: [Brennan Conroy](https://github.com/BrennanConroy)

SignalR을 사용하면 특정 사용자와 관련된 모든 연결뿐만 아니라 명명된 연결 그룹에 메시지를 전송할 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>SignalR의 사용자

SignalR을 사용하면 특정 사용자와 관련된 모든 연결에 메시지를 전송할 수 있습니다. SignalR은 기본적으로 연결과 관련된 `ClaimsPrincipal`의 `ClaimTypes.NameIdentifier`를 사용자 식별자로 사용합니다. SignalR 앱에서 단일 사용자는 여러 개의 연결을 가질 수 있습니다. 예를 들어 사용자가 데스크톱은 물론 휴대폰에서도 연결했을 수 있습니다. 각 장치마다 별도의 SignalR 연결을 갖지만, 이는 모두 동일한 사용자와 관련된 연결입니다. 사용자에게 메시지가 전송되면 해당 사용자와 연관된 모든 연결에서 메시지를 수신합니다. 연결의 사용자 식별자는 허브의 `Context.UserIdentifier` 속성을 이용해서 접근할 수 있습니다.

다음 예제와 같이 허브 메서드에서 `User` 함수에 사용자 식별자를 전달해서 특정 사용자에게 메시지를 전송합니다.

> [!NOTE]
> 사용자 식별자는 대소문자를 구분합니다.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>SignalR의 그룹

그룹은 이름이 연관된 연결들의 컬렉션입니다. 그룹의 모든 연결에 메시지를 전송할 수 있습니다. 그룹이 연결이나 다수의 연결에 전송하기에 적합한 방식인 이유는 그룹이 응용 프로그램에 의해 관리되기 때문입니다. 연결은 여러 그룹의 멤버일 수 있습니다. 따라서 그룹은 각 방을 그룹으로 표현할 수 있는 채팅 같은 응용 프로그램에 이상적입니다. `AddToGroupAsync` 및 `RemoveFromGroupAsync` 메서드를 통해서 연결을 그룹에 추가하거나 제거할 수 있습니다.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

연결이 다시 연결되더라도 그룹의 멤버 자격은 유지되지 않습니다. 연결이 다시 설정될 때 그룹에 다시 추가해야 합니다. 응용 프로그램이 다수의 서버로 확장되는 경우 필요한 정보를 사용할 수 없기 때문에, 그룹의 구성원 수를 계산하는 것은 불가능합니다.

사용 하 여 그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 [인증 및 권한 부여](xref:signalr/authn-and-authz) ASP.NET Core의 기능입니다. 해당 그룹에 전송 된 메시지는 해당 그룹에 대 한 유효한 자격 증명이 그룹에만 사용자를 추가 하는 경우 권한이 있는 사용자만으로 그러나 그룹을 보안 기능이 없습니다. 인증 클레임에 그룹 만료 및 해지 등 하지 않는 기능이 있습니다. 그룹에 액세스 하려면 사용자의 권한을 해지 되 면 수동으로 탐지 하 고 그룹에서 제거 해야 합니다.

> [!NOTE]
> 그룹 이름은 대소문자를 구분합니다.

## <a name="related-resources"></a>관련 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시하기](xref:signalr/publish-to-azure-web-app)
