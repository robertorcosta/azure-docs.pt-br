---
title: Solucionar problemas de TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba o que fazer sobre vários problemas usando certificados TLS/SSL com o MSAL. Biblioteca Objective-C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881070"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Como: Solucionar problemas do MSAL para problemas iOS e macOS TLS/SSL

Este artigo fornece informações para ajudá-lo a solucionar problemas que você pode encontrar ao usar a [Microsoft Authentication Library (MSAL) para iOS e macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de rede

**Erro -1200**: "Ocorreu um erro de SSL e uma conexão segura com o servidor não pode ser feita."

Este erro significa que a conexão não está segura. Ocorre quando um certificado é inválido. Para obter mais informações, incluindo qual servidor está `NSURLErrorFailingURLErrorKey` falhando `userInfo` na verificação TLS, consulte no dicionário do objeto de erro.

Este erro é da biblioteca de rede da Apple. Uma lista completa de códigos de erro NSURL está em NSURLError.h nos SDKs macOS e iOS. Para obter mais detalhes sobre esse erro, consulte [Códigos de erro do sistema de carregamento de URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemas de certificado

Se a URL que fornece um certificado inválido se conectar ao servidor que você pretende usar como parte do fluxo de autenticação, um bom começo para diagnosticar o problema é testar a URL com um serviço de validação SSL, como o [Teste do Servidor SSL.](https://www.ssllabs.com/ssltest/analyze.html) Ele testa o servidor contra uma ampla gama de cenários e navegadores e verifica muitas vulnerabilidades conhecidas.

Por padrão, o novo recurso [ATS (App Transport Security, segurança](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) de transporte de aplicativos) da Apple aplica políticas de segurança mais rigorosas a aplicativos que usam certificados TLS/SSL. Alguns sistemas operacionais e navegadores da Web começaram a aplicar algumas dessas políticas por padrão. Por razões de segurança, recomendamos que você não desabilite o ATS.

Os certificados que usam hashes SHA-1 têm vulnerabilidades conhecidas. A maioria dos navegadores da Web modernos não permite certificados com hashes SHA-1.

## <a name="captive-portals"></a>Portais cativos

Um portal cativo apresenta uma página web para um usuário quando eles acessam pela primeira vez uma rede Wi-Fi e ainda não tiveram acesso a essa rede. Intercepta seu tráfego de internet até que o usuário satisfaça os requisitos do portal. Erros de rede porque o usuário não pode se conectar aos recursos da rede são esperados até que o usuário se conecte através do portal.

## <a name="next-steps"></a>Próximas etapas

Conheça os [portais cativos](https://en.wikipedia.org/wiki/Captive_portal) e o novo recurso [ATS (App Transport Security, segurança](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) de transporte de aplicativos) da Apple.
