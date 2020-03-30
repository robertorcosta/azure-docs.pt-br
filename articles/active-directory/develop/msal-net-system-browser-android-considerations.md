---
title: Considerações do navegador do sistema Android Xamarin (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar navegadores de sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132605"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Considerações do navegador do sistema Android Xamarin para o uso de MSAL.NET

Este artigo discute o que você deve considerar quando você usa o navegador do sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

Começando com MSAL.NET 2.4.0 Preview, MSAL.NET suporta navegadores diferentes do Chrome. Ele não requer mais que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que você use navegadores que suportam guias personalizadas. Aqui estão alguns exemplos desses navegadores:

| Navegadores que têm suporte a guias personalizadas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de identificar navegadores que oferecem suporte a guias personalizadas, nossos testes indicam que alguns navegadores que não suportam guias personalizadas também funcionam para autenticação. Esses navegadores incluem Opera, Opera Mini, InBrowser e Maxthon. 

## <a name="tested-devices-and-browsers"></a>Dispositivos e navegadores testados
A tabela a seguir lista os dispositivos e navegadores que foram testados para compatibilidade de autenticação.

| Dispositivo | Navegador     |  Result  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Aprovado|
| Huawei/One+ | Microsoft Edge\* | Aprovado|
| Huawei/One+ | Firefox\* | Aprovado|
| Huawei/One+ | Corajoso\* | Aprovado|
| Um+ | Ecosia\* | Aprovado|
| Um+ | Kiwi\* | Aprovado|
| Huawei/One+ | Opera | Aprovado|
| Huawei | Operamini | Aprovado|
| Huawei/One+ | Navegador InBrowser | Aprovado|
| Um+ | Maxthon | Aprovado|
| Huawei/One+ | DuckDuckGo | Autenticação cancelada pelo usuário|
| Huawei/One+ | Navegador UC | Autenticação cancelada pelo usuário|
| Um+ | Golfinho | Autenticação cancelada pelo usuário|
| Um+ | Navegador CM | Autenticação cancelada pelo usuário|
| Huawei/One+ | Nenhum instalado | AndroidActivityNotFound exceção|

\*Suporta guias personalizadas

## <a name="known-issues"></a>Problemas conhecidos

Se o usuário não tiver nenhum navegador ativado `AndroidActivityNotFound` no dispositivo, MSAL.NET lançará uma exceção.  
  - **Atenuação**: Peça ao usuário para ativar um navegador em seu dispositivo. Recomendo um navegador que suporte guias personalizados.

Se a autenticação falhar (por exemplo, se a autenticação `AuthenticationCanceled MsalClientException`for lançada com DuckDuckGo), MSAL.NET retornarão . 
  - **Problema raiz**: Um navegador que suporta guias personalizadas não estava habilitado no dispositivo. Autenticação lançada com um navegador que não conseguia completar a autenticação. 
  - **Atenuação**: Peça ao usuário para ativar um navegador em seu dispositivo. Recomendo um navegador que suporte guias personalizados.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações e exemplos de código, consulte [Escolhendo entre um navegador web incorporado e um navegador de sistema no Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) e [Embedded versus System Web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui).  