---
title: incluir arquivo
description: incluir arquivo
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335085"
---
| Recurso | Limite |
| --- | --- |
| Recursos do Azure Front Door por assinatura | 100 |
| Hosts front-end, que inclui domínios personalizados por recurso | 500 |
| Regras de roteamento por recurso | 500 |
| Pools back-end por recurso | 50 |
| Extremidades traseiras por piscina back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| URLs em uma única chamada de expurgo de cache | 100 |
| Regras de firewall do aplicativo Web personalizadas por política | 100 |
| Política de firewall de aplicativos da Web por assinatura | 100 |
| Condições de correspondência de firewall de aplicativos da Web por regra personalizada | 10 |
| Os intervalos de endereçoIP do firewall do aplicativo Web por condição de correspondência | 600 |
| Valores de correspondência de string de firewall de aplicativos da Web por condição de correspondência | 10 |
| Comprimento do valor da linha de linha de seqüência de string do aplicativo web | 256 |
| Comprimento do nome do nome do arquivo do corpo do firewall do aplicativo web | 256 |
| Comprimento do nome do cabeçalho HTTP do aplicativo da Web | 256 |
| Comprimento do nome do aplicativo da Web | 256 |
| Firewall de aplicativo web HTTP solicitação tamanho do corpo inspecionado | 128 KB |
| Comprimento do corpo de resposta personalizada do firewall do aplicativo web | 2 KB |

### <a name="timeout-values"></a>Valores de tempo para doado
#### <a name="client-to-front-door"></a>Cliente para o Front Door
* O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta da Frente para back-end de aplicação
* Se a resposta for uma resposta em pedaços, um 200 é devolvido se ou quando o primeiro pedaço for recebido.
* Depois que a solicitação HTTP é encaminhada para a parte traseira, a Porta da Frente espera por 30 segundos para o primeiro pacote a partir do back-end. Em seguida, ele retorna um erro 503 para o cliente. Esse valor é configurável através do campo sendRecvTimeoutSeconds na API.
    * Para cenários de cache, esse tempo não é configurável e, portanto, se uma solicitação for armazenada em cache e levar mais de 30 segundos para o primeiro pacote da Porta da Frente ou do backend, então um erro de 504 é devolvido ao cliente. 
* Depois que o primeiro pacote é recebido da parte traseira, a Porta da Frente espera por 30 segundos em um tempo indomparado. Em seguida, ele retorna um erro 503 para o cliente. Este valor de tempo não é configurável.
* A porta da frente para o tempo de intervalo da sessão tcp back-end é de 90 segundos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com codificação de transferência em pedaços (CTE) | Sem http chunking |
| ---- | ------- | ------- |
| **Download** | Não há limite para o tamanho do download. | Não há limite para o tamanho do download. |
| **Upload** |    Não há limite, desde que cada upload de CTE seja inferior a 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
* Tamanho máximo da URL - 8.192 bytes - Especifica o comprimento máximo da URL bruta (esquema + nome do host + porta + caminho + seqüência de consulta da URL)
* Tamanho máximo da corda de consulta - 4.096 bytes - Especifica o comprimento máximo da seqüência de consulta, em bytes.
* Tamanho máximo do cabeçalho de resposta HTTP da URL do teste de saúde - 4.096 bytes - Especificado o comprimento máximo de todos os cabeçalhos de resposta dos testes de saúde. 
