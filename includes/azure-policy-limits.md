---
title: incluir arquivo
description: incluir arquivo
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 04e607296478520298a2febab61a7edac911a59c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131635"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Uma entrada do _Escopo_ significa a assinatura ou o [grupo de gerenciamento](../articles/governance/management-groups/overview.md).

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de política | 500 |
| Escopo | Definições de iniciativa | 100 |
| Locatário | Definições de iniciativa | 1,000 |
| Escopo | Atribuições de iniciativa ou política | 100 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de iniciativa ou política | Exclusões (notScopes) | 400 |
| Regra de política | Condicionais aninhadas | 512 |
| Tarefa de correção | Recursos | 500 |
