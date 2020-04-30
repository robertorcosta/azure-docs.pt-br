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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81736275"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do lote do Azure por região por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 90-900 | Contate o suporte |
| Núcleos de baixa prioridade por conta do Lote | 10-100 | Contate o suporte |
| Trabalhos **[ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e agendas de trabalho por conta do lote (trabalhos**concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Núcleos por conta do Lote | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Para solicitar um aumento além desse limite, entre em contato com o suporte do Azure.

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Para nos ajudar a gerenciar melhor a capacidade durante o pandemia global Health, as cotas de núcleo padrão para novas contas do lote em algumas regiões e para alguns tipos de assinatura foram reduzidas do intervalo acima de valores, em alguns casos até zero núcleos. Quando você cria uma nova conta do lote, [Verifique a cota principal](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento de cota de núcleo](../articles/batch/batch-quota-limit.md#increase-a-quota), se necessário. 
