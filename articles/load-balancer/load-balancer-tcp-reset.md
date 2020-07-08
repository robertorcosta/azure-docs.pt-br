---
title: Redefinição de TCP do Load Balancer quando ocioso no Azure
titleSuffix: Azure Load Balancer
description: Com este artigo, saiba mais sobre Azure Load Balancer com pacotes TCP RST bidirecionais no tempo limite de ociosidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: 68714053ac92faf8550a3e5f83a526afa1222971
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808474"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Load Balancer com Redefinição de TCP quando ocioso

Você pode usar o [Standard Load Balancer](load-balancer-standard-overview.md) para criar um comportamento de aplicativo mais previsível para seus cenários, permitindo a Redefinição de TCP no modo ocioso para uma determinada regra. O comportamento de padrão do Load Balancer é remover fluxos silenciosamente quando o tempo limite de ociosidade de um fluxo for atingido.  Habilitar esse recurso fará o Load Balancer enviar Redefinições de TCP bidirecionais (pacote TCP RST) no tempo limite de ociosidade.  Isso informará os pontos de extremidade do aplicativo de que a conexão atingiu o tempo limite e não é mais utilizável.  Pontos de extremidade podem estabelecer imediatamente uma nova conexão se necessário.

![Redefinição de TCP do Balanceador de Carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Alterar esse comportamento padrão e habilitar envio de Redefinições de TCP no tempo limite de ociosidade em regras NAT de entrada, regras de balanceamento de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando habilitado por regra, o Load Balancer enviará Redefinições de TCP (pacotes TCP RST) para os pontos de extremidade do cliente e do servidor no momento do tempo limite de ociosidade para todos os fluxos correspondentes.

Os pontos de extremidade que recebem pacotes TCP RST fecham imediatamente o soquete correspondente. Isso fornece uma notificação imediata para os pontos de extremidade que a liberação da conexão ocorreu e qualquer comunicação futura na mesma conexão TCP falhará.  Aplicativos podem limpar conexões quando o soquete é fechado e restabelecer as conexões conforme necessário, sem esperar o tempo limite eventual da conexão TCP.

Para muitos cenários, isso pode reduzir a necessidade de enviar keepalives de TCP (ou de camada de aplicativo) para atualizar o tempo limite de um fluxo. 

Se as durações ociosas excederem aquelas permitidas pela configuração ou seu aplicativo apresentar um comportamento indesejado com Redefinições de TCP habilitadas, você ainda precisará usar keepalives de TCP (ou keepalives da camada de aplicativo) para monitorar a atividade das conexões TCP.  Além disso, keepalives também podem ser úteis para quando a conexão é transmitida com proxy em algum lugar no caminho, particularmente em keepalives de camada de aplicativo.  

Examine cuidadosamente todo o cenário de ponta a ponta para decidir se é vantagem habilitar Redefinições de TCP, ajustando o tempo limite de ociosidade, e se podem ser necessárias etapas adicionais para garantir o comportamento do aplicativo desejado.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Habilitar Redefinição de TCP após tempo limite de ociosidade

Usando a API versão 2018-07-01, você pode habilitar o envio de Redefinições de TCP bidirecionais após tempo limite de ociosidade em uma base por regra:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Disponibilidade da região

Disponível em todas as regiões.

## <a name="limitations"></a>Limitações

- TCP RST é enviado somente durante a conexão TCP no estado estabelecido.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md).
- [Configurar TCP RST no tempo limite ocioso](load-balancer-tcp-idle-timeout.md)
