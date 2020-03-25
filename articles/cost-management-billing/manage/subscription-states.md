---
title: Estados de assinatura do Azure
description: Este artigo descreve os diferentes estados e status de uma assinatura do Azure.
keywords: Estado status da assinatura do Azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78270227"
---
# <a name="azure-subscription-states"></a>Estados de assinatura do Azure

Este artigo descreve os vários estados que uma assinatura do Azure pode ter. Você encontrará esses estados exibidos como **Status** nas áreas de assinatura do portal do Azure.

| Estado da assinatura | Descrição |
|-------------| ----------------|
| **Ativo** | Sua assinatura do Azure está ativa. Use a assinatura para implantar novos recursos e gerenciar os existentes.|
| **Excluída** | Sua assinatura do Azure foi excluída juntamente com todos os recursos/dados subjacentes. |
| **Desabilitado** | Sua assinatura do Azure está desabilitada e não pode mais ser usada para criar ou gerenciar recursos do Azure. Enquanto estão nesse estado, suas máquinas virtuais são desalocadas, os endereços IP temporários são liberados, o armazenamento é somente leitura e outros serviços são desabilitados. Uma assinatura pode ser desabilitada devido aos seguintes motivos: Seu crédito pode ter expirado. Você pode ter atingido o limite de gastos. Você tem uma fatura vencida. O limite do seu cartão de crédito foi excedido. Ou então, ele foi explicitamente desabilitado ou cancelado. Dependendo do tipo de assinatura, uma assinatura pode permanecer desabilitada entre 1 e 90 dias. Após esse período, ela é excluída permanentemente. Para obter mais informações, confira [Reativar uma assinatura desabilitada do Azure](subscription-disabled.md). |
| **Expirada** | Sua assinatura do Azure expirou porque foi cancelada. Você pode reativar uma assinatura expirada. Para obter mais informações, confira [Reativar uma assinatura desabilitada do Azure](subscription-disabled.md).|
| **Vencido** | Sua assinatura do Azure tem um pagamento pendente. Sua assinatura ainda está ativa, mas o não pagamento de dívidas pode resultar na desabilitação da assinatura. Para obter mais informações, confira [Resolver o saldo vencido da sua assinatura do Azure](resolve-past-due-balance.md). |
