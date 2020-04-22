---
title: incluir arquivo
description: incluir arquivo
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736275"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do Azure Batch por região por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 90-900 | Contate o suporte |
| Núcleos de baixa prioridade por conta do Lote | 10-100 | Contate o suporte |
| **[Empregos ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta de lote (trabalhos**concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Núcleos por conta do Lote | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Para solicitar um aumento além desse limite, entre em contato com o Suporte azure.

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Para nos ajudar a gerenciar melhor a capacidade durante a pandemia global de saúde, as cotas principais padrão para novas contas batch em algumas regiões e para alguns tipos de assinatura foram reduzidas da faixa acima de valores, em alguns casos para zero núcleos. Ao criar uma nova conta de lote, [verifique sua cota principal](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento de cota principal,](../articles/batch/batch-quota-limit.md#increase-a-quota)se necessário. 
