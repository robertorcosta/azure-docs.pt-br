---
title: Monitoramento de métricas e logs na Porta da Frente do Azure| Microsoft Docs
description: Este artigo descreve as diferentes métricas e registros de acesso que o Azure Front Door suporta
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471720"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitoramento de métricas e logs na Porta da Frente do Azure

Usando o Azure Front Door, você pode monitorar os recursos das seguintes maneiras:

- **Métricas**. Aporta Frontal do Azure atualmente tem sete métricas para visualizar contadores de desempenho.
- **Logs**. Os registros de atividade e diagnóstico permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

### <a name="metrics"></a>Métricas

As métricas são um recurso para certos recursos do Azure que permitem visualizar contadores de desempenho no portal. As métricas disponíveis da Porta da Frente:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado a partir da solicitação do cliente recebida pelo Front Door até que o cliente reconheceu o último byte de resposta da Porta da Frente. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Registros de atividades

Os registros de atividades fornecem informações sobre as operações feitas na Porta da Frente. Eles também determinam o que, quem e quando para quaisquer operações de gravação (put, post ou delete) tomadas na Porta da Frente.

>[!NOTE]
>Os registros de atividade não incluem operações de leitura (get). Eles também não incluem operações que você executa usando o portal Azure ou a API de gerenciamento original.

A atividade de acesso faz login na porta da frente ou em todos os registros de seus recursos do Azure no Azure Monitor. Para exibir logs de atividade:

1. Selecione a instância da porta da frente.
2. Selecione **o registro de atividades**.

    ![Log de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um escopo de filtragem e, em seguida, **selecione Aplicar**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Registros de diagnóstico
Os registros de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para auditoria e solução de problemas. Os registros de diagnóstico diferem dos registros de atividade.

Os registros de atividades fornecem insights sobre as operações feitas nos recursos do Azure. Os registros de diagnóstico fornecem informações sobre as operações realizadas pelo seu recurso. Para obter mais informações, consulte [os registros de diagnóstico do Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

![Logs de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar registros de diagnóstico para a porta da frente:

1. Selecione a porta da frente do Azure.

2. Escolha **configurações de diagnóstico**.

3. Selecione **Ativar diagnóstico**. Arquive registros de diagnóstico junto com métricas para uma conta de armazenamento, transmita-os para um centro de eventos ou envie-os para logs do Monitor do Azure.

A porta da frente fornece atualmente registros de diagnóstico (em lote por hora). Os registros de diagnóstico fornecem solicitações individuais de API com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| Nome de backendHost | Se a solicitação estava sendo encaminhada para um backend, este campo representa o nome de hospedagem do backend. Este campo ficará em branco se a solicitação for redirecionada ou encaminhada para um cache regional (quando o cache estiver ativado para a regra de roteamento). |
| CacheStatus | Para cenários de cache, este campo define o acerto/erro de cache no POP |
| ClientIp | Endereço IP do cliente que fez a solicitação. Se houve um cabeçalho X-Forwarded-For na solicitação, então o IP do cliente será escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornou do proxy. |
| Detalhes do Status http | Status resultante na solicitação. O significado desse valor de string pode ser encontrado em uma tabela de referência status. |
| HttpVersion | Tipo de solicitação ou conexão. |
| POP | Nome curto da borda onde o pedido desembarcou. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o órgão de solicitação. |
| Requesturi | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor backend como resposta.  |
| Nome de regra de roteamento | O nome da regra de roteamento que o pedido correspondia. |
| Securityprotocol | A versão do protocolo TLS/SSL usada pela solicitação ou nula se não houver criptografia. |
| Escudo de Origem de SenttoOrigin | Campo booleano representando se houve uma falha de cache no primeiro ambiente e a solicitação foi enviada para o cache regional. Ignore este campo se a regra de roteamento for um redirecionamento ou quando não tiver o cache ativado. |
| TimeTaken | O tempo que a ação levou, em milissegundos. |
| Referência de rastreamento | A seqüência de referência única que identifica uma solicitação atendida pelo Front Door, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registros de acesso para uma solicitação específica. |
| UserAgent | O tipo de navegador que o cliente usou. |

**Nota:** Para várias configurações de roteamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, sentToOriginShield e campo POP podem responder com valores diferentes. A tabela abaixo explica os diferentes valores que esses campos terão para vários cenários:

| Cenários | Contagem de entradas de log | POP | Nome de backendHost | Escudo de Origem de SenttoOrigin | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de roteamento sem cache ativado | 1 | Código EDGE POP | Backend onde o pedido foi encaminhado | Falso | CONFIG_NOCACHE |
| Regra de roteamento com cache ativado. Cache atingido na borda POP | 1 | Código EDGE POP | Vazio | Falso | Atingido |
| Regra de roteamento com cache ativado. Cache falha na borda POP, mas cache hit no cache pai POP | 2 | 1. Código EDGE POP</br>2. Código POP do cache dos pais | 1. Arquivo POP de cache painome de host</br>2. Vazio | 1. Verdade</br>2. Falso | 1. MISS</br>2. PARTIAL_HIT |
| Regra de roteamento com cache ativado. Cache falha tanto na borda quanto no cache pai POP | 2 | 1. Código EDGE POP</br>2. Código POP do cache dos pais | 1. Arquivo POP de cache painome de host</br>2. Backend que ajuda a preencher cache | 1. Verdade</br>2. Falso | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>Próximas etapas

- [Crie um perfil da Porta da Frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)
