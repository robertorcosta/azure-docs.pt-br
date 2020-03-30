---
title: Como marcar uma máquina virtual do Azure Linux
description: Saiba como marcar uma máquina virtual Linux do Azure criada no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 3dd46f0c1c1e36e0373311ce6dcbf5d2f155dacc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616270"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Linux no Azure
Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Linux no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure suporta até 50 tags por grupo de recursos e recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcando com a CLI do Azure

Para começar, será necessário ter a [CLI do Azure](/cli/azure/install-azure-cli) mais recente instalada e registrada em uma conta do Azure usando [az login](/cli/azure/reference-index#az-login).

É possível exibir todas as propriedades de uma determinada Máquina Virtual, incluindo as marcas, usando este comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Para adicionar uma nova marcação de VM usando a CLI do Azure, use o comando `azure vm update`, juntamente com o parâmetro de marcação **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover as marcas, use o parâmetro **--remove** no comando `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Agora que aplicamos marcas aos nossos recursos por meio do CLI do Azure e do Portal, vamos examinar os detalhes de uso para ver as marcas no portal de cobrança.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager][Azure Resource Manager Overview] e [Usando marcas para organizar os Recursos do Azure][Using Tags to organize your Azure Resources].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [Noções básicas de sua fatura do Azure][Understanding your Azure Bill] e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
