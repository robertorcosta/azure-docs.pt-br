---
title: Gerenciar protocolos e cifras no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como gerenciar protocolos (TLS) e codificações (DES) no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335879"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerenciar protocolos e cifras no Gerenciamento de API do Azure

O Gerenciamento de API do Azure dá suporte a várias versões do protocolo TLS para os lados do cliente e do back-end, bem como para a cifra 3DES.

Este guia mostra como gerenciar a configuração de protocolos e cifras para uma instância de Gerenciamento de API do Azure.

![Gerenciar protocolos e cifras no APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você precisa ter:

* Uma instância de Gerenciamento de API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerenciar protocolos TLS e a cifra 3DES

1. Navegue até sua **instância de Gerenciamento de API** no portal do Azure.
2. Selecione **configurações de protocolo** no menu.  
3. Habilite ou desabilite os protocolos ou cifras desejados.
4. Clique em **Save** (Salvar). As alterações serão aplicadas em uma hora.  

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.