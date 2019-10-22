---
title: Exemplo de script da CLI do Azure – Obter um grupo de recursos gerenciado e redimensionar VMs | Microsoft Docs
description: Fornece um script de exemplo da CLI do Azure que obtém um grupo de recursos gerenciados em um Aplicativo Gerenciado do Azure. O script redimensiona as VMs.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 7218e2fd033614b5e9f77cca34a5db82e3571094
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330137"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obter recursos em um grupo de recursos gerenciado e redimensionar VMs com a CLI do Azure

Este script recupera os recursos de um grupo de recursos gerenciado e redimensiona as VMs nesse grupo de recursos.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | Liste os aplicativos gerenciados. Forneça os valores da consulta para destacar os resultados. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | Liste os recursos. Forneça um grupo de recursos e os valores da consulta para destacar os resultados. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
