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
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="da79d-103">SignalR에서 사용자 및 그룹 관리</span><span class="sxs-lookup"><span data-stu-id="da79d-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="da79d-104">작성자: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="da79d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="da79d-105">SignalR을 사용하면 특정 사용자와 관련된 모든 연결뿐만 아니라 명명된 연결 그룹에 메시지를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="da79d-106">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="da79d-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="da79d-107">SignalR의 사용자</span><span class="sxs-lookup"><span data-stu-id="da79d-107">Users in SignalR</span></span>

<span data-ttu-id="da79d-108">SignalR을 사용하면 특정 사용자와 관련된 모든 연결에 메시지를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="da79d-109">SignalR은 기본적으로 연결과 관련된 `ClaimsPrincipal`의 `ClaimTypes.NameIdentifier`를 사용자 식별자로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="da79d-110">SignalR 앱에서 단일 사용자는 여러 개의 연결을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="da79d-111">예를 들어 사용자가 데스크톱은 물론 휴대폰에서도 연결했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="da79d-112">각 장치마다 별도의 SignalR 연결을 갖지만, 이는 모두 동일한 사용자와 관련된 연결입니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="da79d-113">사용자에게 메시지가 전송되면 해당 사용자와 연관된 모든 연결에서 메시지를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="da79d-114">연결의 사용자 식별자는 허브의 `Context.UserIdentifier` 속성을 이용해서 접근할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="da79d-115">다음 예제와 같이 허브 메서드에서 `User` 함수에 사용자 식별자를 전달해서 특정 사용자에게 메시지를 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="da79d-116">사용자 식별자는 대소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="da79d-117">SignalR의 그룹</span><span class="sxs-lookup"><span data-stu-id="da79d-117">Groups in SignalR</span></span>

<span data-ttu-id="da79d-118">그룹은 이름이 연관된 연결들의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="da79d-119">그룹의 모든 연결에 메시지를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="da79d-120">그룹이 연결이나 다수의 연결에 전송하기에 적합한 방식인 이유는 그룹이 응용 프로그램에 의해 관리되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="da79d-121">연결은 여러 그룹의 멤버일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="da79d-122">따라서 그룹은 각 방을 그룹으로 표현할 수 있는 채팅 같은 응용 프로그램에 이상적입니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="da79d-123">`AddToGroupAsync` 및 `RemoveFromGroupAsync` 메서드를 통해서 연결을 그룹에 추가하거나 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="da79d-124">연결이 다시 연결되더라도 그룹의 멤버 자격은 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="da79d-125">연결이 다시 설정될 때 그룹에 다시 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="da79d-126">응용 프로그램이 다수의 서버로 확장되는 경우 필요한 정보를 사용할 수 없기 때문에, 그룹의 구성원 수를 계산하는 것은 불가능합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="da79d-127">사용 하 여 그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 [인증 및 권한 부여](xref:signalr/authn-and-authz) ASP.NET Core의 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="da79d-128">해당 그룹에 전송 된 메시지는 해당 그룹에 대 한 유효한 자격 증명이 그룹에만 사용자를 추가 하는 경우 권한이 있는 사용자만으로</span><span class="sxs-lookup"><span data-stu-id="da79d-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="da79d-129">그러나 그룹을 보안 기능이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-129">However, groups are not a security feature.</span></span> <span data-ttu-id="da79d-130">인증 클레임에 그룹 만료 및 해지 등 하지 않는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="da79d-131">그룹에 액세스 하려면 사용자의 권한을 해지 되 면 수동으로 탐지 하 고 그룹에서 제거 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="da79d-132">그룹 이름은 대소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="da79d-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="da79d-133">관련 자료</span><span class="sxs-lookup"><span data-stu-id="da79d-133">Related resources</span></span>

* [<span data-ttu-id="da79d-134">시작</span><span class="sxs-lookup"><span data-stu-id="da79d-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="da79d-135">허브</span><span class="sxs-lookup"><span data-stu-id="da79d-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="da79d-136">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="da79d-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
