---
title: ASP.NET Core에 대 한 클라이언트 IP 수신
author: damienbod
description: 승인 된 IP 주소의 목록에 대해 원격 IP 주소의 유효성을 검사 하려면 미들웨어 또는 작업 필터를 작성 하는 방법에 알아봅니다.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223927"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core에 대 한 클라이언트 IP 수신

하 여 [Damien Bowden](https://twitter.com/damien_bod) 고 [Tom Dykstra](https://github.com/tdykstra)
 
이 아티클에서 IP 수신 (라고도: 화이트 리스트) ASP.NET Core 앱을 구현 하는 세 가지 방법에 설명 합니다. 사용할 수 있습니다.

* 모든 요청 원격 IP 주소를 확인 하는 미들웨어입니다.
* 특정 컨트롤러 또는 작업 메서드에 대 한 요청 된 원격 IP 주소를 확인 하려면 작업 필터입니다.
* Razor 페이지 필터를 Razor 페이지에 대 한 요청 된 원격 IP 주소를 확인 합니다.

각 경우에서는 승인 된 클라이언트 IP 주소를 포함 하는 문자열을 앱 설정에 저장 됩니다. 미들웨어 또는 필터를 목록으로 문자열을 구문 분석 하 고 원격 IP 목록 인지 확인 합니다. 그렇지 않은 경우는 HTTP 403 사용 권한 없음 상태 코드가 반환 됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>수신

목록에 구성 된 *appsettings.json* 파일입니다. 세미콜론으로 구분 된 목록 이므로 IPv4 및 IPv6 주소를 포함할 수 있습니다.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>미들웨어

`Configure` 메서드는 미들웨어를 추가 하 고 수신 문자열을 생성자 매개 변수에서를 전달 합니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

미들웨어를 배열에 문자열을 구문 분석 및 배열에 있는 원격 IP 주소를 찾습니다. 원격 IP 주소가 없는 경우 미들웨어는 HTTP 401 사용할 수 없음 반환 합니다. 이 유효성 검사 프로세스는 HTTP Get 요청에 대해 무시 됩니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>작업 필터

특정 컨트롤러 또는 작업 메서드에서 동안만 수신 하려는 경우에 작업 필터를 사용 합니다. 예를 들면 다음과 같습니다. 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

작업 필터는 서비스 컨테이너에 추가 됩니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

필터 컨트롤러나 작업 메서드에 사용할 수 있습니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

샘플 앱에서 필터에 적용 되는 `Get` 메서드. 전송 하 여 앱을 테스트 하면를 `Get` 특성 클라이언트 IP 주소 유효성을 검사 하는 API를 요청 합니다. 다른 HTTP 메서드를 사용 하 여 API를 호출 하 여 테스트할 때 미들웨어 클라이언트 IP의 유효성 검사 됩니다.

## <a name="razor-pages-filter"></a>Razor 페이지 필터링 

Razor 페이지 앱에 대 한를 수신 하려는 경우에 Razor 페이지 필터를 사용 합니다. 예를 들면 다음과 같습니다. 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

이 필터는 MVC 필터 컬렉션에 추가 하 여 사용 됩니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

앱을 실행 하는 Razor 페이지를 요청 하는 경우 Razor 페이지 필터 클라이언트 IP의 유효성 검사 됩니다.

## <a name="next-steps"></a>다음 단계

[ASP.NET Core 미들웨어에 자세히 알아보려면](xref:fundamentals/middleware/index)합니다.
