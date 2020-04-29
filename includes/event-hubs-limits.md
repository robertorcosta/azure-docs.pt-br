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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75901670"
---
As tabelas a seguir fornecem cotas e limites específicos para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

Os limites a seguir são comuns em camadas básica, Standard e dedicada. 

| Limite | Escopo | Anotações | Valor |
| --- | --- | --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |Assinatura |- |100 |
| Número de hubs de eventos por namespace |Namespace |As solicitações subsequentes para a criação de um novo hub de eventos são rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Tamanho máximo do nome de um hub de eventos |Entidade |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Unidades de produtividade máxima |Namespace |Exceder o limite de unidade de produtividade faz com que os dados sejam restringidos e gera uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de produtividade para uma camada Standard, arquivo uma [solicitação de suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|As solicitações subsequentes para a criação da regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de rede virtual (VNet) e de configuração de IP | Entidade | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hubs de eventos básico e padrão-cotas e limites
| Limite | Escopo | Anotações | Basic | Standard |
| --- | --- | --- | -- | --- |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade | &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por hub de eventos |Entidade | &nbsp; |1 |20 |
| Número de conexões AMQP por namespace |Namespace |As solicitações subsequentes para conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |100 |5\.000|
| Período de retenção máximo dos dados do evento |Entidade | &nbsp; |1 dia |Um a sete dias |
|Apache Kafka namespace habilitado|Namespace |Namespace de hubs de eventos transmite aplicativos usando o protocolo Kafka |Não | Sim |
|Capturar |Entidade | Quando habilitado, micro lotes no mesmo fluxo |Não |Sim |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hubs de Eventos Dedicados-cotas e limites
A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de 4 horas de uso. A camada dedicada oferece todos os recursos do plano padrão, mas com capacidade e limites de escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Namespaces | 50 por CU |
| Hubs de Eventos |  1000 por namespace |
| Eventos de entrada | Incluído |
| Tamanho da mensagem | 1 MB |
| Partições | 2000 por CU |
| Grupos de consumidores | Nenhum limite por CU, 1000 por Hub de eventos |
| Conexões orientadas | 100.000 incluídos |
| Retenção de mensagem | 90 dias, 10 TB incluídos por CU |
| Capturar | Incluído |
