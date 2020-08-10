---
title: incluir arquivo
description: incluir arquivo
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038490"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Uma entrada do _Escopo_ significa a assinatura ou o [grupo de gerenciamento](../articles/governance/management-groups/overview.md).

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de política | 500 |
| Escopo | Definições de iniciativa | 200 |
| Locatário | Definições de iniciativa | 2\.500 |
| Escopo | Atribuições de iniciativa ou política | 200 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 1000 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de iniciativa ou política | Exclusões (notScopes) | 400 |
| Regra de política | Condicionais aninhadas | 512 |
| Tarefa de correção | Recursos | 1000 |
