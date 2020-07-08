---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317577"
---
A tabela a seguir fornece cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

Os limites a seguir são comuns nos níveis básico e standard. 

| Limite | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |Subscription |- |100 |
| Número de hubs de eventos por namespace |Namespace |As solicitações subsequentes para a criação de um hub de eventos são rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Tamanho máximo do nome de um hub de eventos |Entidade |- | 256 caracteres |
| Tamanho máximo de um nome de grupo de consumidores |Entidade |- | 256 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Unidades de produtividade máxima |Namespace |Exceder o limite de unidades de produtividade vai causar a limitação dos seus dados e vai gerar uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de produtividade para um nível Standard, preencha uma [solicitação de suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|As solicitações subsequentes de criação de regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de VNet (rede virtual) e de configuração de IP | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hubs de Eventos Básico e Standard – Cotas e limites
| Limite | Escopo | Observações | Basic | Standard |
| --- | --- | --- | -- | --- |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade | &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por hub de eventos |Entidade | &nbsp; |1 |20 |
| Número de conexões AMQP por namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |100 |5\.000|
| Período de retenção máximo dos dados do evento |Entidade | &nbsp; |1 dia |Um a sete dias |
|Namespace habilitado para Apache Kafka|Namespace |O namespace de Hubs de Eventos transmite aplicativos usando o protocolo Kafka |Não | Sim |
|Capturar |Entidade | Quando habilitado, coloca em microlotes no mesmo fluxo |Não |Sim |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hubs de Eventos Dedicados – Cotas e limites
A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de quatro horas de uso. O nível Dedicado oferece todos os recursos do plano Standard, mas com capacidade em escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Recurso | limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Namespaces | 50 por CU |
| Hubs de Eventos |  1\.000 por namespace |
| Eventos de entrada | Incluso |
| Tamanho da mensagem | 1 MB |
| Partições | 2\.000 por CU |
| Grupos de consumidores | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 100.000 incluídos |
| Retenção de mensagem | 90 dias, 10 TB incluídos por CU |
| Capturar | Incluso |
