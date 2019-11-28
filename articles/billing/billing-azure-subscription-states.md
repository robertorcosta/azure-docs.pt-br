---
title: Estados de assinatura do Azure
description: Descreve os diferentes estados\status de uma assinatura do Azure
keywords: Estado status da assinatura do Azure
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 741e7cf0869e36b5788d0a883a4e982caf041512
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224101"
---
# <a name="azure-subscription-states"></a>Estados de assinatura do Azure
Este artigo descreve os vários estados que uma assinatura do Azure pode ter. Você os encontrará exibidos como “Status” nas folhas de assinatura.

| Estado da assinatura | DESCRIÇÃO |
|-------------| ----------------|
| **Ativo** | Sua assinatura do Azure está ativa. Você pode usar essa assinatura para implantar recursos novos e existentes.|
| **Vencido** | Sua assinatura do Azure tem um pagamento pendente. Sua assinatura ainda está ativa, mas o não pagamento de dívidas pode resultar na desabilitação da assinatura. [Resolver o saldo vencido de sua assinatura do Azure.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Desabilitado** | Sua assinatura do Azure está desabilitada e não pode mais ser usada para criar ou gerenciar recursos do Azure. Enquanto estão nesse estado, suas máquinas virtuais são desalocadas, os endereços IP temporários são liberados, o armazenamento é somente leitura e outros serviços são desabilitados. A assinatura pode ser desabilitada porque seu crédito pode ter expirado, você pode ter atingido seu limite de gastos, você tem uma fatura vencida, o limite do seu cartão de crédito foi excedido ou se ela foi explicitamente desabilitada/cancelada. Dependendo do tipo de assinatura e do motivo da desabilitação, uma assinatura pode continuar desabilitada de 1 a 90 dias. Após esse período, ela será excluída permanentemente. [Reativar uma assinatura do Azure desabilitada.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Excluída** | Sua assinatura do Azure foi excluída juntamente com todos os recursos/dados subjacentes. |
