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
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="0ce41-103">ASP.NET Core에서 권한 부여 소개</span><span class="sxs-lookup"><span data-stu-id="0ce41-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="0ce41-104">권한 부여 결정 하는 프로세스를 가리킵니다. 사용자가 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="0ce41-105">예를 들어, 관리자 문서 라이브러리 만들기, 문서 추가, 문서 편집 및 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="0ce41-106">라이브러리를 사용 하 여 작업 관리자가 아닌 사용자는 문서를 읽을 권한이.</span><span class="sxs-lookup"><span data-stu-id="0ce41-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="0ce41-107">권한 부여 부분도 있고 독립 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="0ce41-108">그러나 권한 부여는 인증 메커니즘을 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="0ce41-109">인증은 사용자가 익명인 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="0ce41-110">인증 현재 사용자에 대 한 하나 이상의 id를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-110">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="0ce41-111">인증 형식</span><span class="sxs-lookup"><span data-stu-id="0ce41-111">Authorization types</span></span>

<span data-ttu-id="0ce41-112">ASP.NET Core 권한 부여 제공을 간단 하 고 선언적인 [역할](xref:security/authorization/roles) 와 다양 한 [정책 기반](xref:security/authorization/policies) 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-112">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="0ce41-113">권한 부여 요구 사항에서 표현 됩니다 및 요구 사항에 대 한 사용자의 클레임을 평가 하는 처리기.</span><span class="sxs-lookup"><span data-stu-id="0ce41-113">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="0ce41-114">명령적 검사는 간단한 정책 또는 사용자 id 및 사용자가 액세스 하려고 하는 리소스의 속성을 모두 평가 하는 정책을 기반으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-114">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="0ce41-115">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="0ce41-115">Namespaces</span></span>

<span data-ttu-id="0ce41-116">권한 부여 구성 요소를 포함 하는 `AuthorizeAttribute` 및 `AllowAnonymousAttribute` 특성에 포함 됩니다는 `Microsoft.AspNetCore.Authorization` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-116">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="0ce41-117">설명서를 참조 하세요 [단순 권한 부여](xref:security/authorization/simple)합니다.</span><span class="sxs-lookup"><span data-stu-id="0ce41-117">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
