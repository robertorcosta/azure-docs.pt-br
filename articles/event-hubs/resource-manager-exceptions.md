---
title: Azure Event Hubs - Exceções do Gerenciador de Recursos | Microsoft Docs
description: Lista de exceções do Azure Event Hubs apareceu pelo Azure Resource Manager e sugeriu ações.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936081"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs - Exceções do Gerenciador de Recursos
Este artigo lista as exceções geradas ao interagir com o Azure Event Hubs usando o Azure Resource Manager - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há atualizações.

As seções a seguir fornecem várias exceções/erros que são fornecidos através do Azure Resource Manager.

## <a name="error-code-conflict"></a>Código de erro: Conflito

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflito | 40300 | O número máximo de recursos do tipo EventHub foi atingido ou excedido. Real: #, Max permitiu: # | O namespace atingiu sua [cota](event-hubs-quotas.md) para o número de Hubs de Eventos que ele pode conter. | Exclua quaisquer hubs de eventos não utilizados ou estranhos do namespace ou considere atualizar para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| Conflito | none | A configuração de recuperação de desastres (DR) não pode ser excluída porque a replicação está em andamento. Falha ou quebra o emparelhamento antes de tentar excluir a configuração DR. | [A replicação geoDR](event-hubs-geo-dr.md) está em andamento, então a configuração não pode ser excluída neste momento. | Para desbloquear a exclusão da configuração, aguarde até que a replicação seja concluída, acione um failover ou quebre o emparelhamento GeoDR. |
| Conflito | none | A atualização do namespace falhou com o conflito no backend. | Outra operação está sendo feita neste namespace. | Aguarde até que a operação atual seja concluída e, em seguida, tente novamente. |

## <a name="error-code-429"></a>Código de erro: 429

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | none | Provisionamento de namespace em transição | Outra operação está sendo feita neste namespace. | Aguarde até que a operação atual seja concluída e, em seguida, tente novamente. |
| 429 | none | Operação de recuperação de desastres em andamento. | Uma operação [GeoDR](event-hubs-geo-dr.md) está sendo feita neste namespace ou emparelhamento. | Aguarde até que a operação geodr atual seja concluída e, em seguida, tente novamente. |

## <a name="error-code-badrequest"></a>Código de erro: BadRequest

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount não pode ser alterado para um hub de eventos. | O nível básico ou padrão do Azure Event Hubs não suporta a alteração de partições. | Crie um novo hub de eventos com o número desejado de partições no seu namespace de nível básico ou padrão. A escala de partição é suportada para [clusters dedicados](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | O valor '#' para MessageRetentionInDays não é válido para o nível Básico. o valor não pode exceder '1' dia(s). | Os namespaces de eventos de nível básico só suportam a retenção de mensagens de até 1 dia. | Se mais de um dia de retenção de mensagens for desejado, [crie um namespace padrão do Event Hubs](event-hubs-create.md). | 
| BadRequest | none | O nome especificado não está disponível. | Os nomes do namespace devem ser únicos e o nome especificado já foi tomado. | Se você é o proprietário do namespace existente com o nome especificado, você pode excluí-lo, o que causará perda de dados. Então, tente novamente com o mesmo nome. Se o namespace não for seguro para excluir (ou você não é o proprietário), escolha outro nome de namespace. |
| BadRequest | none | A assinatura especificada atingiu sua cota de namespaces. | Sua assinatura atingiu a [cota](event-hubs-quotas.md) para o número de namespaces que ele pode manter. | Considere excluir namespaces não utilizados nesta assinatura, criar outra assinatura ou atualizar para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| BadRequest | none | Não é possível atualizar um namespace que é secundário | O namespace não pode ser atualizado porque é o namespace secundário em um [emparelhamento GeoDR](event-hubs-geo-dr.md). | Se for o caso, faça a alteração para o espaço de nome principal neste emparelhamento. Caso contrário, quebre o emparelhamento GeoDR para fazer a mudança. |
| BadRequest | none | Não é possível definir Auto-Inflar no SKU básico | O Auto-Inflate não pode ser ativado em espaços de nome de nível básico do Event Hubs. | Para [ativar o Auto Inflate](event-hubs-auto-inflate.md) em um namespace, certifique-se de que é de nível padrão. |
| BadRequest | none | Não há capacidade suficiente para criar o namespace. Entre em contato com o administrador do Event Hubs. | A região selecionada está em capacidade e mais namespaces não podem ser criados. | Selecione outra região para abrigar seu namespace. |
| BadRequest | none | A operação não pode ser feita no tipo de entidade 'ConsumerGroup' porque o namespace 'namespace namespace name' está usando o nível 'Básico'.  | Os namespaces do Event Hubs de nível básico têm um [quota]((event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) de um grupo de consumidores (o padrão). A criação de mais grupos de consumidores não é suportada. | Continue usando o grupo de consumidores padrão ($Default), ou se mais forem necessários, considere usar um namespace padrão do Event Hubs. | 
| BadRequest | none | O namespace 'namespace name' não existe. | O espaço de nome fornecido não foi encontrado. | Verifique se o nome do namespace está correto e pode ser encontrado em sua assinatura. Se não for, [crie um namespace do Event Hubs](event-hubs-create.md). | 
| BadRequest | none | A propriedade de localização do recurso não corresponde ao seu namespace contendo. | A criação de um hub de eventos em uma região específica falhou porque não correspondia à região do namespace. | Tente criar o hub de eventos na mesma região que o namespace. | 

## <a name="error-code-internal-server-error"></a>Código de erro: Erro interno do servidor

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | none | Erro Interno do Servidor. | O serviço Event Hubs teve um erro interno. | Tente novamente a operação de falha. Se a operação continuar a falhar, entre em contato com o suporte. |