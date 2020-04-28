---
title: Azure Key Vault como fonte da grade de eventos
description: Descreve as propriedades e o esquema fornecido para eventos de Azure Key Vault com a grade de eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458242"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault como fonte da grade de eventos

Este artigo fornece as propriedades e o esquema para eventos no [Azure Key Vault](../key-vault/index.yml), atualmente em visualização. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta de Azure Key Vault gera os seguintes tipos de evento:

| Nome completo do evento | Nome de exibição do evento | Descrição |
| ---------- | ----------- |---|
| Microsoft. keyvault. CertificateNewVersionCreated | Nova versão de certificado criada | Disparado quando um novo certificado ou nova versão do certificado é criado. |
| Microsoft. keyvault. CertificateNearExpiry | Certificado próximo à expiração | Disparado quando a versão atual do certificado está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. CertificateExpired | O Certificado Expirou | Disparado quando o certificado expira. |
| Microsoft. keyvault. KeyNewVersionCreated | Nova versão de chave criada | Disparado quando uma nova versão de chave ou nova chave é criada. |
| Microsoft. keyvault. KeyNearExpiry | Chave próxima à expiração | Disparado quando a versão atual de uma chave está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. keyvenceu | Chave expirada | Disparado quando uma chave expira. |
| Microsoft. keyvault. SecretNewVersionCreated | Nova versão secreta criada | Disparado quando uma nova versão secreta ou nova secreta é criada. |
| Microsoft. keyvault. SecretNearExpiry | Segredo próximo da expiração | Disparado quando a versão atual de um segredo está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. SecretExpired | Segredo expirado | Disparado quando um segredo expira. |

### <a name="event-examples"></a>Exemplos de eventos

O exemplo a seguir mostra o esquema para **Microsoft. keyvault. SecretNewVersionCreated**:

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
| id | cadeia de caracteres | A ID do objeto que disparou este evento |
| vaultName | cadeia de caracteres | O nome do cofre de chaves do objeto que disparou este evento |
| objectType | cadeia de caracteres | O tipo do objeto que disparou este evento |
| objectName | cadeia de caracteres | O nome do objeto que disparou este evento |
| version | cadeia de caracteres | A versão do objeto que disparou este evento |
| nbf | número | A data de não-antes, em segundos, desde 1970-01-01T00:00:00Z do objeto que disparou este evento |
| exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que disparou este evento |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Title  |Descrição  |
|---------|---------|
| [Monitorando Key Vault eventos com a grade de eventos do Azure](../key-vault/general/event-grid-overview.md) | Visão geral da integração de Key Vault com a grade de eventos. |
| [Tutorial: criar e monitorar eventos de Key Vault com a grade de eventos](../key-vault/general/event-grid-tutorial.md) | Saiba como configurar notificações de grade de eventos para Key Vault. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para saber mais sobre a integração de Key Vault com a grade de eventos, consulte [monitorando Key Vault com a grade de eventos do Azure (versão prévia)](../key-vault/general/event-grid-overview.md).
* Para obter um tutorial sobre a integração Key Vault com a grade de eventos, consulte [receber e responder a notificações do Key Vault com a grade de eventos do Azure (versão prévia)](../key-vault/general/event-grid-tutorial.md).
* Para obter diretrizes adicionais para Key Vault e a automação do Azure, consulte:
    - [O que é o Azure Key Vault?](../key-vault/general/overview.md)
    - [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](../key-vault/general/event-grid-overview.md)
    - [Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure (versão prévia)](../key-vault/general/event-grid-tutorial.md)
    - [Visão geral da Automação do Azure](../automation/index.yml)
