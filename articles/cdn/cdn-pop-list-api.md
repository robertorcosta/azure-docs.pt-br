---
title: Recupere a lista IP pop atual para Azure CDN| Microsoft Docs
description: Saiba como recuperar a lista POP atual.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299243"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperar a lista IP POP atual para CDN do Azure

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperar a lista ip atual do Verizon POP para CDN do Azure

Você pode usar a API REST para recuperar o conjunto de IPs para o ponto da Verizon dos servidores de presença (POP). Esses servidores POP fazem solicitações para servidores de origem que estão associados com os pontos de extremidade do Azure Content Delivery Network (CDN) em um perfil Verizon (**Azure CDN padrão da Verizon** ou **Premium do Azure CDN da Verizon**). Observe que esse conjunto de IPs é diferente dos IPs que um cliente veria ao fazer solicitações aos POPs. 

Para obter a sintaxe da operação da API REST para recuperar a lista POP, consulte [Nós de Borda - Lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperar a lista IP atual do Microsoft POP para CDN do Azure

Para bloquear seu aplicativo para aceitar o tráfego apenas do Azure CDN da Microsoft, você precisará configurar ACLs IP para o seu backend. Você também pode restringir o conjunto de valores aceitos para o cabeçalho 'X-Forwarded-Host' enviado pelo Azure CDN da Microsoft. Estas etapas são detalhadas conforme abaixo:

Configure o IP ACLing para que seus backends aceitem o tráfego do CDN do Azure cDN do espaço de endereço IP backend da Microsoft e apenas dos serviços de infra-estrutura do Azure. 

* CDN do Azure CDN do espaço IP backend IPv4 da Microsoft: 147.243.0.0/16
* CDN azure do espaço IP backend IPv6 da Microsoft: 2a01:111:2050::/44

As marcas IP Ranges e Service para serviços Microsoft podem ser encontradas [aqui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso de uso típico

Por motivos de segurança, use essa lista de IPs para impor que as solicitações para o seu servidor de origem sejam feitas apenas a partir de um POP válido da Verizon. Por exemplo, se alguém descobrir o nome do host ou o endereço IP do servidor de origem de um ponto de extremidade de CDN, seria possível fazer solicitações diretamente ao servidor de origem, ignorando os recursos de dimensionamento e segurança fornecidos pela CDN do Azure. Ao definir os IPs na lista retornada como os únicos IPs permitidos em um servidor de origem, esse cenário pode ser evitado. Para garantir que você tenha a lista POP mais recente, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a API REST, consulte [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
