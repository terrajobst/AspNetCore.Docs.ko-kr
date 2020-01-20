---
title: 지원 되는 플랫폼 ASP.NET Core Blazor
author: guardrex
description: ASP.NET Core Blazor에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160134"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>지원 되는 플랫폼 ASP.NET Core Blazor

작성자: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>브라우저 요구 사항

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

| 브라우저                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 현재               |
| Mozilla Firefox                  | 현재               |
| Google Chrome (Android 포함) | 현재               |
| Safari (iOS 포함)            | 현재               |
| Microsoft Internet Explorer      | 지원 되지 않음&dagger; |

&dagger;Microsoft Internet Explorer는 [Weasembmbmbmbmbmba](https://webassembly.org)를 지원 하지 않습니다.

### <a name="opno-locblazor-server"></a>Blazor 서버

| 브라우저                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 현재    |
| Mozilla Firefox                  | 현재    |
| Google Chrome (Android 포함) | 현재    |
| Safari (iOS 포함)            | 현재    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;추가 polyfills 필요 합니다. 예를 들어 [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 약속을 추가할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/hosting-models>
