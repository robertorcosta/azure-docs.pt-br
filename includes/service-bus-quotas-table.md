---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651641"
---
A tabela a seguir lista informações de cotas específicas para mensagens do Azure Service Bus. Para obter informações sobre preços e outras cotas para Ônibus de Serviço, consulte [preços de Ônibus de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome da cota | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número máximo de namespaces básicos ou padrão por assinatura do Azure |Namespace |As solicitações subseqüentes para espaços de nomes básicos ou padrão adicionais são rejeitadas pelo portal Azure. |100|
| Número máximo de namespaces Premium por assinatura do Azure |Namespace |As solicitações subseqüentes para espaços de nome Premium adicionais são rejeitadas pelo portal. |100 |
| Fila ou tamanho do tópico |Entidade |Definido após a criação da fila ou tópico. <br/><br/> As mensagens recebidas subseqüentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No SKU Premium e no SKU padrão com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) ativado, o tamanho máximo da fila ou do tópico é de 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |Solicitações subseqüentes para conexões adicionais são rejeitadas, e uma exceção é recebida pelo código de chamada. As operações REST não contam para conexões TCP simultâneas. |NetMessaging: 1.000.<br /><br />AMQP: 5.000. |
| Número de solicitações simultâneas de recebimento em uma fila, tópico ou entidade de assinatura |Entidade |As solicitações de recebimento subseqüentes são rejeitadas, e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5.000 |
| Número de tópicos ou filas por namespace |Namespace |Solicitações subsequentes de criação de um novo tópico ou fila no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |10.000 para o nível Básico ou Padrão. O número total de tópicos e filas em um namespace deve ser menor ou igual a 10.000. <br/><br/>Para o nível Premium, 1.000 por unidade de mensagens (MU). O limite máximo é de 4.000. |
| Número de [tópicos particionados ou filas](/azure/service-bus-messaging/service-bus-partitioning) por namespace |Namespace |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se for chamada da API de gerenciamento, a exceção **QuotaExceededException** é recebida pelo código de chamada. |Níveis básicos e padrão: 100.<br/><br/>As entidades particionadas não são suportadas no nível [Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Cada fila particionada ou tópico conta para a cota de 1.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |- |50 caracteres. |
| Tamanho máximo de uma [mensagem ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma [id de sessão](/dotnet/api/microsoft.azure.servicebus.message.sessionid) de mensagem | Entidade |- | 128 |
| Tamanho da mensagem para uma entidade de fila, tópico ou assinatura |Entidade |As mensagens recebidas que excedem essas cotas são rejeitadas, e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para [nível padrão,](../articles/service-bus-messaging/service-bus-premium-messaging.md)1 MB para [nível Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, esse limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho no saco de propriedade: **byte/int. MaxValue**.<br /><br />tamanho máximo da propriedade no recipiente de propriedades: nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho da propriedade da mensagem para uma entidade de fila, tópico ou assinatura |Entidade | A exceção **SerializationException** é gerada. |O tamanho máximo da propriedade da mensagem para cada propriedade é de 32.000. O tamanho acumulado de todas as propriedades não pode exceder 64.000. Esse limite se aplica a todo o cabeçalho do [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem propriedades do usuário e propriedades do sistema, como [SequenceNumber,](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Número de assinaturas por tópico |Entidade |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |2.000 por tópico para o nível Padrão. |
| Número de filtros SQL por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados, e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados, e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho dos filtros ou ações SQL |Namespace |Os pedidos subseqüentes de criação de filtros adicionais são rejeitados, e uma exceção é recebida pelo código de chamada. |Comprimento máximo da seqüência da condição do filtro: 1.024 (1 K).<br /><br />Comprimento máximo da seqüência de ação de regra: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por namespace, fila ou tópico |Entidade, namespace |Os pedidos subsequentes de criação de regras adicionais são rejeitados, e uma exceção é recebida pelo código de chamada. |Número máximo de regras por tipo de entidade: 12. <br /><br /> As regras configuradas em um namespace do Service Bus aplicam-se a todos os tipos: filas, tópicos. |
| Número de mensagens por transação | Transação | Mensagens recebidas adicionais são rejeitadas e uma exceção informando "Não é possível enviar mais de 100 mensagens em uma única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()**. |
| Número de regras de filtro de rede virtual e IP | Namespace | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
