---
title: Monitoramento de métricas e logs na porta frontal do Azure | Microsoft Docs
description: Este artigo descreve as diferentes métricas e logs de acesso aos quais a porta frontal do Azure dá suporte
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471720"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitoramento de métricas e logs na porta frontal do Azure

Usando a porta frontal do Azure, você pode monitorar os recursos das seguintes maneiras:

- **Métricas**. Atualmente, a porta frontal do Azure tem sete métricas para exibir os contadores de desempenho.
- **Logs**. Os logs de atividade e diagnóstico permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

### <a name="metrics"></a>Métricas

As métricas são um recurso para determinados recursos do Azure que permitem Exibir contadores de desempenho no Portal. A seguir estão as métricas de porta frontal disponíveis:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado a partir da solicitação do cliente recebida pela porta frontal até que o cliente confirme o último byte de resposta da porta frontal. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Logs de atividade

Os logs de atividade fornecem informações sobre as operações realizadas na porta da frente. Eles também determinam o que, quem e quando para qualquer operação de gravação (put, post ou Delete) realizada na porta frontal.

>[!NOTE]
>Os logs de atividade não incluem operações de leitura (Get). Eles também não incluem operações que você executa usando o portal do Azure ou a API de gerenciamento original.

Acesse os logs de atividade em sua porta de front-end ou todos os logs dos recursos do Azure no Azure Monitor. Para exibir logs de atividade:

1. Selecione sua instância de porta frontal.
2. Selecione **log de atividades**.

    ![Log de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um escopo de filtragem e, em seguida, selecione **aplicar**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Logs de diagnóstico
Os logs de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria e solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades.

Os logs de atividades fornecem informações sobre as operações realizadas nos recursos do Azure. Os logs de diagnóstico fornecem informações sobre as operações executadas pelo recurso. Para obter mais informações, consulte [Azure monitor logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

![Logs de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar os logs de diagnóstico para sua porta frontal:

1. Selecione sua porta frontal do Azure.

2. Escolha **configurações de diagnóstico**.

3. Selecione **Ativar diagnóstico**. Arquive logs de diagnóstico junto com as métricas para uma conta de armazenamento, transmita-os para um hub de eventos ou envie-os para Azure Monitor logs.

Atualmente, a porta frontal fornece logs de diagnóstico (em lote). Os logs de diagnóstico fornecem solicitações de API individuais com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se a solicitação estava sendo encaminhada para um back-end, esse campo representa o nome do host do back-end. Esse campo ficará em branco se a solicitação tiver sido redirecionada ou encaminhada para um cache regional (quando o Caching estiver habilitado para a regra de roteamento). |
| CacheStatus | Para cenários de cache, esse campo define o impacto/perda do cache no POP |
| ClientIp | Endereço IP do cliente que fez a solicitação. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status. |
| HttpVersion | Tipo de solicitação ou conexão. |
| POP | Nome curto da borda em que a solicitação descarregou. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento com a qual a solicitação foi correspondida. |
| SecurityProtocol | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia. |
| SentToOriginShield | Campo booliano que representa se houve uma perda de cache no primeiro ambiente e a solicitação foi enviada para o cache regional. Ignore este campo se a regra de roteamento for um redirecionamento ou quando o cache não estiver habilitado. |
| TimeTaken | O período de tempo que a ação levou, em milissegundos. |
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente, também enviada como o cabeçalho X-Azure-ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| UserAgent | O tipo de navegador que o cliente usou. |

**Observação:** Para várias configurações de roteamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, sentToOriginShield e POP Field podem responder com valores diferentes. A tabela abaixo explica os valores diferentes, que esses campos terão para vários cenários:

| Cenários | Contagem de entradas de log | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de roteamento sem Caching habilitado | 1 | Código POP do Edge | Back-end em que a solicitação foi encaminhada | Falso | CONFIG_NOCACHE |
| Regra de roteamento com Caching habilitado. Cache atingido no POP de borda | 1 | Código POP do Edge | Vazio | Falso | CONTADOR |
| Regra de roteamento com Caching habilitado. Erro de cache no POP de borda, mas acesso ao cache no POP do cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. nome do host POP do cache pai</br>2. vazio | 1. verdadeiro</br>2. false | 1. PERDA</br>2. PARTIAL_HIT |
| Regra de roteamento com Caching habilitado. Perda de cache na borda e no POP de cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. nome do host POP do cache pai</br>2. back-end que ajuda a preencher o cache | 1. verdadeiro</br>2. false | 1. PERDA</br>2. PERDA |


## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de porta de front-end](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)
