---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901670"
---
As tabelas a seguir fornecem cotas e limites específicos para [o Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

Os seguintes limites são comuns entre os níveis básico, padrão e dedicado. 

| Limite | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |Subscription |- |100 |
| Número de hubs de eventos por namespace |Namespace |Os pedidos subsequentes de criação de um novo hub de eventos são rejeitados. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Tamanho máximo do nome de um hub de eventos |Entidade |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Unidades de produtividade máxima |Namespace |Exceder o limite da unidade de throughput faz com que seus dados sejam estrangulados e gera uma [exceção ocupada do servidor](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de throughput para um nível Padrão, registre uma [solicitação de suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|Os pedidos subsequentes de criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e CONfig IP | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hubs de Eventos Básico e Padrão - cotas e limites
| Limite | Escopo | Observações | Basic | Standard |
| --- | --- | --- | -- | --- |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade | &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por hub de eventos |Entidade | &nbsp; |1 |20 |
| Número de conexões AMQP por namespace |Namespace |Solicitações subseqüentes para conexões adicionais são rejeitadas, e uma exceção é recebida pelo código de chamada. |100 |5.000|
| Período de retenção máximo dos dados do evento |Entidade | &nbsp; |1 dia |Um a sete dias |
|Apache Kafka habilitado para namespace|Namespace |O namespace do Event Hubs transmite aplicativos usando o protocolo Kafka |Não | Sim |
|Capturar |Entidade | Quando ativado, microlotes no mesmo fluxo |Não |Sim |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hubs de Eventos Dedicados - cotas e limites
A oferta Event Hubs Dedicated é cobrada a um preço fixo mensal, com um mínimo de 4 horas de uso. O nível Dedicated oferece todos os recursos do plano Standard, mas com capacidade de escala corporativa e limites para clientes com cargas de trabalho exigentes. 

| Recurso | limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Namespaces | 50 por |
| Hubs de Eventos |  1000 por namespace |
| Eventos de entrada | Incluso |
| Tamanho da mensagem | 1 MB |
| Partições | 2000 por |
| Grupos de consumidores | Sem limite por, 1000 por hub de eventos |
| Conexões orientadas | 100.000 incluídos |
| Retenção de mensagem | 90 dias, 10 TB incluídos por |
| Capturar | Incluso |
