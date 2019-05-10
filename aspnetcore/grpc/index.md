---
title: ASP.NET Core에서 gRPC 소개
author: juntaoluo
description: Kestrel 서버 및 ASP.NET Core 스택을 사용하는 gRPC 서비스에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085549"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a><span data-ttu-id="b63a4-103">ASP.NET Core에서 gRPC 소개</span><span class="sxs-lookup"><span data-stu-id="b63a4-103">Introduction to gRPC on ASP.NET Core</span></span>

<span data-ttu-id="b63a4-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b63a4-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b63a4-105">[gRPC](https://grpc.io/docs/guides/)는 언어 제약이 없는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span> <span data-ttu-id="b63a4-106">gRPC 기본 사항에 대한 자세한 내용은 [gRPC 설명서 페이지](https://grpc.io/docs/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b63a4-106">For more on gRPC fundamentals, see the [gRPC documentation page](https://grpc.io/docs/).</span></span>

<span data-ttu-id="b63a4-107">gRPC의 주요 이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-107">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="b63a4-108">최신 고성능 경량 RPC 프레임워크.</span><span class="sxs-lookup"><span data-stu-id="b63a4-108">Modern high-performance lightweight RPC framework.</span></span>
* <span data-ttu-id="b63a4-109">기본적으로 프로토콜 버퍼를 사용하는 계약 우선 API 개발로, 언어 제약이 없는 구현이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-109">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="b63a4-110">강력한 형식의 서버 및 클라이언트를 생성하는 여러 언어에 사용할 수 있는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-110">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="b63a4-111">클라이언트, 서버 및 양방향 스트리밍 호출을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-111">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="b63a4-112">Protobuf 이진 serialization을 통한 네트워크 사용량 감소.</span><span class="sxs-lookup"><span data-stu-id="b63a4-112">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="b63a4-113">이러한 이점으로 인해 gRPC가 다음과 같은 분야에 이상적이 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-113">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="b63a4-114">효율성이 중요한 경량 마이크로 서비스.</span><span class="sxs-lookup"><span data-stu-id="b63a4-114">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="b63a4-115">개발을 위해 여러 언어가 필요한 다언어 시스템.</span><span class="sxs-lookup"><span data-stu-id="b63a4-115">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="b63a4-116">스트리밍 요청 또는 응답을 처리해야 하는 지점 간 실시간 서비스.</span><span class="sxs-lookup"><span data-stu-id="b63a4-116">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

<span data-ttu-id="b63a4-117">C# 구현은 현재 공식 [gRPC 페이지](https://grpc.io/docs/quickstart/csharp.html)에서 사용할 수 있지만, 현재 구현은 C로 작성된 네이티브 라이브러리(gRPC [C-core](https://grpc.io/blog/grpc-stacks))를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-117">While a C# implementation is currently available on the official [gRPC page](https://grpc.io/docs/quickstart/csharp.html), the current implementation relies on the native library written in C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span></span> <span data-ttu-id="b63a4-118">완전하게 관리되는 Kestrel HTTP 서버와 ASP.NET Core 스택을 기반으로 하는 새 구현을 제공하기 위한 작업이 현재 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-118">Work is currently in progress to provide a new implementation based on the Kestrel HTTP server and the ASP.NET Core stack that is fully managed.</span></span> <span data-ttu-id="b63a4-119">다음 문서는 이 새 구현을 통해 gRPC 서비스를 빌드하는 방법을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="b63a4-119">The following documents provide an introduction to building gRPC services with this new implementation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b63a4-120">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b63a4-120">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>