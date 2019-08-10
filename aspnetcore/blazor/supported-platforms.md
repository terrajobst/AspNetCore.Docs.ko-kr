---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 01f3a55a8536feedf713e07ea3724a0bc51e7c63
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "68948253"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 지원 플랫폼

[Luke Latham](https://github.com/guardrex)으로

## <a name="browser-requirements"></a>브라우저 요구 사항

### <a name="blazor-client-side"></a>Blazor 클라이언트 쪽

| 브라우저                          | 버전               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 현재               |
| Mozilla Firefox                  | 현재               |
| Google Chrome (Android 포함) | 현재               |
| Safari (iOS 포함)            | 현재               |
| Microsoft Internet Explorer      | 지원 되지 않음&dagger; |

&dagger;Microsoft Internet Explorer는 [Weasembmbambmba](https://webassembly.org)를 지원 하지 않습니다.

### <a name="blazor-server-side"></a>Blazor 서버 쪽

| 브라우저                          | 버전    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 현재    |
| Mozilla Firefox                  | 현재    |
| Google Chrome (Android 포함) | 현재    |
| Safari (iOS 포함)            | 현재    |
| Microsoft Internet Explorer      | pt&dagger; |

&dagger;추가 polyfills 필요 합니다. 예를 들어 약속을 [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 추가할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/hosting-models>
