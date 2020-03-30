---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334924"
---
| Recurso | Limite | Observação |
| --- | --- | --- |
| Gateway de Aplicativo do Azure |1.000 por assinatura | |
| Configurações de IP front-end |2 |1 pública e 1 privada |
| Portas de front-end |100<sup>1</sup> | |
| Pools de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por pool |1.200 | |
| Ouvintes HTTP |200<sup>1</sup> |Limitado a 100 ouvintes ativos que estão direcionando o tráfego. Ouvintes ativos = número total de ouvintes - ouvintes não ativos.<br>Se uma configuração padrão dentro de uma regra de roteamento for definida como tráfego de rota (por exemplo, ela tiver um ouvinte, um pool de back-end e configurações HTTP) isso também conta como ouvinte.|
| Regras de balanceamento de carga HTTP |100<sup>1</sup> | |
| Configurações de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| Certificados SSL |100<sup>1</sup> |1 por ouvinte HTTP |
| Tamanho máximo do certificado SSL |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificados de autenticação |100 | |
| Certificados raiz confiáveis |100 | |
| Solicitar tempo mínimo |1 segundo | |
| Solicitação máximo de tempo limite |24 horas | |
| Número de sites |100<sup>1</sup> |1 por ouvinte HTTP |
| Mapas de URL por ouvinte |1 | |
| Regras com base em caminho máximo por mapa de URL|100||
| Configurações de redirecionamento |100<sup>1</sup>| |
| Conexões WebSocket Simultâneas |Gateways médios 20k<br> Grandes gateways 50k| |
| Tamanho máximo da URL|32KB| |
| Tamanho máximo do cabeçalho para HTTP/2 |4kb| |
| Tamanho máximo de upload de arquivo, Padrão |2 GB | |
| Tamanho de carregamento de arquivo máximo WAF |Gateways WAF médios V1, 100 MB<br>V1 Grandes gateways WAF, 500 MB<br>V2 WAF, 750 MB| |
| Limite de tamanho do corpo WAF, sem arquivos|128 KB||
| Regras adutoras máximas de WAF|100||
| Exclusões máximas de WAF|100||

<sup>1</sup> No caso de SKUs habilitados para WAF, recomendamos que você limite o número de recursos para 40 para um desempenho ideal.
