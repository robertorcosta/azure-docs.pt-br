---
title: incluir arquivo
description: incluir arquivo
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080914"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do Azure Batch por região por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 90-900 | Contate o suporte |
| Núcleos de baixa prioridade por conta do Lote | 10-100 | Contate o suporte |
| **[Empregos ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta de lote (trabalhos**concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Núcleos por conta do Lote | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Para solicitar um aumento além desse limite, entre em contato com o Suporte azure.
