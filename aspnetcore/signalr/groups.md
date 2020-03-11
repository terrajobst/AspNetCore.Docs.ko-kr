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
ms.openlocfilehash: 7e8c85dcbc46daa68988374f499f19a9d2cead47
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654249"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>SignalR에서 사용자 및 그룹 관리

만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)

SignalR를 사용 하면 명명 된 연결 그룹 뿐만 아니라 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>SignalR의 사용자

SignalR를 사용 하 여 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다. 기본적으로 SignalR는 연결과 관련 된 `ClaimsPrincipal`의 `ClaimTypes.NameIdentifier`를 사용자 식별자로 사용 합니다. 단일 사용자는 SignalR 앱에 대 한 여러 연결을 가질 수 있습니다. 예를 들어 사용자가 데스크톱은 물론 휴대폰에서도 연결했을 수 있습니다. 각 장치에는 별도의 SignalR 연결이 있지만 모두 동일한 사용자와 연결 됩니다. 사용자에게 메시지가 전송되면 해당 사용자와 연관된 모든 연결에서 메시지를 수신합니다. 허브의 `Context.UserIdentifier` 속성을 사용 하 여 연결에 대 한 사용자 id에 액세스할 수 있습니다.

다음 예제와 같이 허브 메서드에서 사용자 id를 `User` 함수에 전달 하 여 특정 사용자에 게 메시지를 보냅니다.

> [!NOTE]
> 사용자 식별자는 대소문자를 구분합니다.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>SignalR의 그룹

그룹은 이름이 연관된 연결들의 컬렉션입니다. 그룹의 모든 연결에 메시지를 전송할 수 있습니다. 그룹이 연결이나 다수의 연결에 전송하기에 적합한 방식인 이유는 그룹이 응용 프로그램에 의해 관리되기 때문입니다. 연결은 여러 그룹의 멤버일 수 있습니다. 따라서 그룹은 각 방을 그룹으로 표현할 수 있는 채팅 같은 응용 프로그램에 이상적입니다. 연결은 `AddToGroupAsync` 및 `RemoveFromGroupAsync` 방법을 통해 그룹에 추가 하거나 그룹에서 제거할 수 있습니다.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

연결이 다시 연결되더라도 그룹의 멤버 자격은 유지되지 않습니다. 연결이 다시 설정될 때 그룹에 다시 추가해야 합니다. 응용 프로그램이 다수의 서버로 확장되는 경우 필요한 정보를 사용할 수 없기 때문에, 그룹의 구성원 수를 계산하는 것은 불가능합니다.

그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 ASP.NET Core의 [인증 및 권한 부여](xref:signalr/authn-and-authz) 기능을 사용 합니다. 해당 그룹에 대 한 자격 증명이 유효한 경우에만 그룹에 사용자를 추가 하는 경우 해당 그룹에 전송 된 메시지는 권한 있는 사용자로만 이동 합니다. 그러나 그룹은 보안 기능이 아닙니다. 인증 클레임에는 만료 및 해지와 같이 그룹에서 제공 하지 않는 기능이 있습니다. 사용자의 그룹 액세스 권한이 해지 된 경우 해당 사용자를 수동으로 검색 하 여 그룹에서 제거 해야 합니다.

> [!NOTE]
> 그룹 이름은 대소문자를 구분합니다.

## <a name="related-resources"></a>관련 리소스

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
