---
title: incluir arquivo
description: incluir arquivo
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 2a35180e4c5ca26d53be135718e345f9657af6a2
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88655274"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do Lote do Azure por região e por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 90 – 900 | Contate o suporte |
| Núcleos de baixa prioridade por conta do Lote | 10 – 100 | Contate o suporte |
| Trabalhos **[ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e agendas de trabalho por conta do Lote (trabalhos **concluídos** não têm limite) | 100 – 300 | 1\.000<sup>1</sup> |
| Núcleos por conta do Lote | 20 – 100 | 500<sup>1</sup> |

<sup>1</sup>Entre em contato com o Suporte do Azure para solicitar aumentos além deste limite.

> [!IMPORTANT]
> Estamos alterando a maneira de solicitar e gerenciar a cota dedicada.  O total de vCPUs dedicadas é o valor imposto no momento, mas, em breve, as cotas dedicadas serão impostas por série de VM. A cota de baixa prioridade continuará sendo imposta com base no limite total, e não será imposta por séries de VM.

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Para conseguirmos gerenciar melhor as capacidades durante a pandemia global, as cotas de núcleos padrão para novas contas do Lote, em algumas regiões e para alguns tipos de assinaturas, foram reduzidas em relação aos valores indicados acima. Em alguns casos, a redução foi para zero núcleos. Quando você criar uma conta do Lote, [verifique a sua cota de núcleos](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento](../articles/batch/batch-quota-limit.md#increase-a-quota), se necessário. Como alternativa, considere reutilizar contas do Lote que já têm cota suficiente. 
