---
title: incluir arquivo
description: incluir arquivo
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272691"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Uma entrada do _Escopo_ significa a assinatura ou o [grupo de gerenciamento](../articles/governance/management-groups/overview.md).

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de política | 500 |
| Escopo | Definições de iniciativa | 100 |
| Locatário | Definições de iniciativa | 1,000 |
| Escopo | Atribuições de iniciativa ou política | 100 |
| Definição de política | parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | parâmetros | 100 |
| Atribuições de iniciativa ou política | Exclusões (notScopes) | 400 |
| Regra de política | Condicionais aninhadas | 512 |
| Tarefa de correção | Recursos | 1000 |
