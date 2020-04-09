---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986657"
---
Estes exemplos mostram como usar o Azure Policy com assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma marca (usando o efeito modificar) a uma assinatura delegada. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria de atribuições de delegação. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que habilita o diagnóstico nos recursos do Azure Key Vault em assinaturas delegadas (usando o efeito deployIfNotExists). Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para habilitar o diagnóstico em uma assinatura delegada e conecta todas as VMs Windows e Linux ao workspace do Log Analytics criado pela política. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma assinatura delegada. |

