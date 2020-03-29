---
title: Atualizações tLS do Notification Hubs
description: Saiba mais sobre o suporte para TLS nos Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908521"
---
# <a name="transport-layer-security-tls"></a>Protocolo TLS

Para garantir um nível mais alto de segurança, os Hubs de Notificação desabilitarão o suporte para as versões 1.0 e 1.1 do TLS em 30 de abril de 2020. Esses protocolos mais antigos fornecem criptografia fraca, e são vulneráveis a ataques BEAST e POODLE. Os aplicativos implantados em dispositivos com versão 5 ou superior do Android, ou versão 5 ou superior do iOS, não são impactados por essa mudança, pois esses sistemas operacionais suportam o TLS 1.2 e o cliente e o servidor negociarão a versão mais alta suportada mutuamente do protocolo após conexão.

Recomendamos que você revise todos os seus aplicativos que usam hubs de notificação do Azure, para garantir que eles usem as bibliotecas e pilhas TLS mais aplicáveis que suportam o TLS 1.2.

## <a name="update-apps"></a>Atualizar aplicativos

Você pode garantir que seus aplicativos para iOS estejam usando o TLS 1.2 usando o recurso de segurança de rede da Apple chamado App Transport Security (ATS). AtS não pode ser usado para SDKs mais antigos que o iOS 9.0 ou macOS 10.11, e você pode ler mais sobre isso a partir [da documentação da Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Para aplicativos Android que usam as instâncias sslsocket, defina protocolos ativados em cada instância do SSLSocket, conforme observado em [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

A tabela na página de suporte [à compatibilidade de protocolo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ajuda a mapear sistemas operacionais com versões TLS compatíveis.

Para obter mais informações, consulte a visão geral do [suporte aos protocolos TLS no Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos Hubs de Notificação](notification-hubs-push-notification-overview.md)