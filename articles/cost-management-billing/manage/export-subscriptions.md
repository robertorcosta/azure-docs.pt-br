---
title: Exportar as informações de nível superior da assinatura do Azure
description: Descreve como é possível exibir todas as IDs da assinatura do Azure associadas à conta.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b3b2e9b501f2ae103900a085e9b7a4b412efb78e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686829"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportar e exibir as informações da assinatura de nível superior
Caso seja necessário exibir o conjunto de IDs da assinatura associadas às credenciais do usuário, [baixe um arquivo .json com as informações da assinatura no Centro de Contas do Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

O arquivo .json baixado fornece as informações a seguir:
- Email: o endereço de email associado à sua conta.
- Puid: o identificador exclusivo associado à conta de cobrança.
- SubscriptionIds: uma lista das assinaturas que pertencem à conta, enumeradas pela ID da assinatura.

### <a name="subscriptionsjson-sample"></a>exemplo de subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
