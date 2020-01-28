---
title: CLI do Azure – parar e excluir uma máquina virtual em um laboratório
description: Esse script da CLI do Azure interrompe e exclui uma máquina virtual em um laboratório.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 7465770eaeb35772012be2219858ccda512775d6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166403"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usar a CLI do Azure para interromper e excluir uma máquina virtual em um laboratório no Azure DevTest Labs

Esse script da CLI do Azure interrompe e exclui uma VM (máquina virtual) em um laboratório. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Interrompe uma máquina virtual (VM) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Exclui uma VM (máquina virtual) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Encontre exemplos adicionais de script da CLI do Azure Lab Services em [Exemplos da CLI do Azure Lab Services](../samples-cli.md).
