---
title: Monitoramento do Key Vault com a Grade de Eventos do Azure
description: Usar a Grade de Eventos do Azure para se inscrever em eventos do Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 854a489f6381514f80f16665f89042a0e8ed7470
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995754"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)

A integração do Key Vault com a Grade de Eventos encontra-se atualmente em versão prévia. Ela permite que os usuários sejam notificados quando o status de um segredo armazenado no cofre de chaves é alterado. Uma alteração de status é definida como um segredo que está prestes a expirar (dentro de 30 dias após a expiração), um segredo que expirou ou um segredo que tem uma nova versão disponível. Há suporte para as notificações de todos os três tipos de segredos (chave, certificado e segredo).

Os aplicativos podem reagir a esses eventos por meio de arquiteturas modernas sem servidor, sem a necessidade de código complicado ou serviços de sondagem ineficientes e caros. Os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos manipuladores de eventos como o [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) ou até seu próprio Webhook, e você só paga pelo que usa. Para saber mais sobre preços, confira [Preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventos e esquemas do Key Vault

A Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. Os eventos do Key Vault contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento do Key Vault porque a propriedade eventType começa com "Microsoft.KeyVault".

Para obter mais informações, confira o [Esquema de eventos do Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Os eventos de notificação são disparados apenas sobre novas versões de segredos, chaves e certificados. Você precisará primeiro assinar o evento no cofre de chaves para receber essas notificações.

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Os aplicativos que manipulam eventos do Key Vault devem seguir algumas práticas recomendadas:

* Várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos. É importante não supor que os eventos sejam de uma fonte específica, mas verificar o tópico da mensagem para garantir que ela venha do cofre de chaves que você está esperando.
* Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
* Ignore os campos que você não entende.  Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
* Use a correspondência de prefixo e sufixo "subject" para limitar os eventos para um evento específico.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Key Vault](overview.md)
- [Visão geral da Grade de Eventos do Azure](../../event-grid/overview.md)
- Como: [rotear eventos de Key Vault para o runbook de automação (visualização)](event-grid-tutorial.md).
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault (versão prévia)](../../event-grid/event-schema-key-vault.md)
- [Visão geral da Automação do Azure](../../automation/index.yml)
