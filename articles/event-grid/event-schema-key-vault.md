---
title: Azure Key Vault como fonte da Grade de Eventos
description: Descreve as propriedades e o esquema fornecidos para eventos do Azure Key Vault com o Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458242"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault como fonte da Grade de Eventos

Este artigo fornece as propriedades e o esquema para eventos no [Azure Key Vault](../key-vault/index.yml), atualmente em pré-visualização. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta do Azure Key Vault gera os seguintes tipos de eventos:

| Nome completo do evento | Nome de exibição do evento | Descrição |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCriado | Certificado Nova Versão Criada | Acionado quando um novo certificado ou nova versão de certificado é criado. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificado perto do vencimento | Acionado quando a versão atual do certificado está prestes a expirar. (O evento é acionado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.Certificado Expirado | O Certificado Expirou | Acionado quando o certificado expirar. |
| Microsoft.KeyVault.KeyNewVersionCriado | Chave nova versão criada | Acionado quando uma nova chave ou nova versão-chave é criada. |
| Microsoft.KeyVault.KeyNearExpiry | Chave perto do vencimento | Acionado quando a versão atual de uma chave está prestes a expirar. (O evento é acionado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.KeyExpirado | Chave expirada | Acionado quando uma chave expira. |
| Microsoft.KeyVault.SecretNewVersionCriado | Nova versão secreta criada | Acionado quando um novo segredo ou nova versão secreta é criado. |
| Microsoft.KeyVault.SecretNearExpiry | Segredo perto do vencimento | Acionado quando a versão atual de um segredo está prestes a expirar. (O evento é acionado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.SecretExpirado | Segredo Expirado | Acionado quando um segredo expira. |

### <a name="event-examples"></a>Exemplos de eventos

O exemplo a seguir mostra esquema para **Microsoft.KeyVault.SecretNewVersionCriado:**

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| ---------- | ----------- |---|
| id | string | O ID do objeto que desencadeou este evento |
| vaultName | string | O nome do cofre chave do objeto que desencadeou este evento |
| objectType | string | O tipo do objeto que desencadeou este evento |
| Objectname | string | O nome do objeto que desencadeou este evento |
| version | string | A versão do objeto que desencadeou este evento |
| nbf | número | A data não-antes em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |
| exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |

## <a name="tutorials-and-how-tos"></a>Tutoriais e como fazer
|Title  |Descrição  |
|---------|---------|
| [Monitorando eventos do Key Vault com a Grade de Eventos do Azure](../key-vault/general/event-grid-overview.md) | Visão geral da integração do Key Vault com o Event Grid. |
| [Tutorial: Crie e monitore eventos do Key Vault com a Grade de Eventos](../key-vault/general/event-grid-tutorial.md) | Saiba como configurar notificações do Event Grid para key vault. |


## <a name="next-steps"></a>Próximas etapas

* Para uma introdução ao Azure Event Grid, veja [O que é a Grade de Eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura do Azure Event Grid, consulte [o esquema de assinatura do Event Grid](subscription-creation-schema.md).
* Para saber mais sobre a integração do Key Vault com o Event Grid, consulte [Monitorando key vault com a Grade de Eventos do Azure (visualização).](../key-vault/general/event-grid-overview.md)
* Para obter um tutorial sobre a integração do Key Vault com o Event Grid, consulte [Receber e responder às notificações principais do cofre com o Azure Event Grid (visualização)](../key-vault/general/event-grid-tutorial.md).
* Para obter orientação adicional para Key Vault e Azure Automation, consulte:
    - [O que é o Azure Key Vault?](../key-vault/general/overview.md)
    - [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](../key-vault/general/event-grid-overview.md)
    - [Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure (versão prévia)](../key-vault/general/event-grid-tutorial.md)
    - [Visão geral da Automação do Azure](../automation/index.yml)
