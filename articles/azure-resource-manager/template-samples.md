---
title: Exemplos de modelo do Azure Resource Manager
description: Exemplos de modelo do Azure Resource Manager para implantar recursos de gerenciamento, como funções e bloqueios.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390176"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>Modelos do Azure Resource Manager para recursos de gerenciamento

A tabela a seguir contém links para modelos do Azure Resource Manager para recursos fornecidos pelo Resource Manager.

| | |
|-|-|
|**Atribuições de função**||
| [Atribuir função para o grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| Atribui uma função interna a um usuário de um grupo de recursos. |
| [Atribuir função a uma máquina virtual existente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| Atribui uma função interna a um usuário de uma VM existente. |
| [Atribuir função a várias máquinas virtuais](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| Atribui funções internas a usuários em mais de uma máquina virtual. |
| [Atribuir função de assinatura do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| Atribui uma função a um usuário para uma assinatura do Azure. |
|**Definição de função**||
| [Criar definição de função personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| Cria uma nova definição de função em uma assinatura do Azure. |
|**Bloqueio de recurso**||
| [Bloquear grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| Cria um grupo de recursos e aplica um bloqueio **DoNotDelete** para o grupo de recursos. Atribui a função de colaborador a um usuário. |
| | |
