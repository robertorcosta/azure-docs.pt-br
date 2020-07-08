---
title: arquivo de inclusão
description: incluir arquivo
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335085"
---
| Recurso | Limite |
| --- | --- |
| Recursos de porta frontal do Azure por assinatura | 100 |
| Hosts front-end, que incluem domínios personalizados por recurso | 500 |
| Regras de roteamento por recurso | 500 |
| Pools de back-ends por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| URLs em uma única chamada de limpeza de cache | 100 |
| Regras de firewall do aplicativo Web personalizadas por política | 100 |
| Política de firewall do aplicativo Web por assinatura | 100 |
| Condições de correspondência do firewall do aplicativo Web por regra personalizada | 10 |
| Intervalos de endereços IP de firewall do aplicativo Web por condição de correspondência | 600 |
| Valores de correspondência de cadeia de caracteres de firewall do aplicativo Web por condição de correspondência | 10 |
| Comprimento do valor de correspondência da cadeia de caracteres do firewall do aplicativo Web | 256 |
| Comprimento do nome do parâmetro de corpo da POSTAgem do firewall do aplicativo Web | 256 |
| Comprimento do nome do cabeçalho HTTP do firewall do aplicativo Web | 256 |
| Comprimento do nome do cookie do firewall do aplicativo Web | 256 |
| Tamanho do corpo da solicitação HTTP do firewall do aplicativo Web inspecionado | 128 KB |
| Comprimento do corpo da resposta personalizada do firewall do aplicativo Web | 2 KB |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para o Front Door
* O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta frontal para back-end de aplicativo
* Se a resposta for uma resposta em bloco, um 200 será retornado se ou quando a primeira parte for recebida.
* Depois que a solicitação HTTP é encaminhada para o back-end, a porta da frente aguarda 30 segundos para o primeiro pacote do back-end. Em seguida, ele retorna um erro 503 para o cliente. Esse valor é configurável por meio do campo sendRecvTimeoutSeconds na API.
    * Para cenários de cache, esse tempo limite não é configurável e, portanto, se uma solicitação for armazenada em cache e levar mais de 30 segundos para o primeiro pacote da porta frontal ou do back-end, um erro 504 será retornado ao cliente. 
* Depois que o primeiro pacote é recebido do back-end, a porta da frente aguarda 30 segundos em um tempo limite de ociosidade. Em seguida, ele retorna um erro 503 para o cliente. Esse valor de tempo limite não é configurável.
* A porta da frente para o tempo limite da sessão TCP de back-end é de 90 segundos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com a CTE (codificação de transferência em bloco) | Sem agrupamento HTTP |
| ---- | ------- | ------- |
| **Download** | Não há limite para o tamanho do download. | Não há limite para o tamanho do download. |
| **Upload** |    Não há limite, desde que cada upload de CTE seja menor que 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
* Tamanho máximo da URL-8.192 bytes-especifica o comprimento máximo da URL bruta (esquema + nome do host + porta + caminho + cadeia de consulta da URL)
* Tamanho máximo da cadeia de caracteres de consulta-4.096 bytes-especifica o comprimento máximo da cadeia de caracteres de consulta, em bytes.
* Tamanho máximo do cabeçalho de resposta HTTP da URL de investigação de integridade-4.096 bytes-especificado o comprimento máximo de todos os cabeçalhos de resposta das investigações de integridade. 
