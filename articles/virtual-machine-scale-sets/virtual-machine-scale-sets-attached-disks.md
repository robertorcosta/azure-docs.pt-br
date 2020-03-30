---
title: A escala da máquina virtual do Azure define discos de dados anexados
description: Aprenda a usar discos de dados conectados com conjuntos de escala de máquinas virtuais através de contornos de casos de uso específicos.
author: avirishuv
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: avverma
ms.openlocfilehash: 6e39a8ffb24b0cca720890e3d00a55d1e58fadc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123370"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Discos de dados conectados e conjuntos de dimensionamento de máquina virtual do Azure
Para expandir o armazenamento disponível, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure oferecem suporte a instâncias de VM com discos de dados anexados. É possível anexar discos de dados ao criar o conjunto de dimensionamento ou em um já existente.

> [!NOTE]
> Ao criar um conjunto de dimensionamento com discos de dados anexado, você precisa montar e formatar os discos de dentro de uma VM para usá-los (assim como para VMs autônomas do Azure). Uma maneira conveniente de concluir esse processo é usar uma Extensão de script personalizado que chama um script para particionar e formatar todos os discos de dados em uma VM. Para obter exemplos disso, veja [CLI do Azure e ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Criar e gerenciar discos em um conjunto de dimensionamento
Para obter informações detalhadas sobre como criar um conjunto de dimensionamento com discos de dados anexado, preparar e formatar ou adicionar e remover discos de dados, consulte um dos seguintes tutoriais:

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

O restante deste artigo descreve os casos de uso específico, como clusters do Service Fabric que requerem discos de dados ou anexar discos de dados existente com conteúdo para um conjunto de dimensionamento.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Criar um cluster do Service Fabric com discos de dados anexados
Cada [tipo de](../service-fabric/service-fabric-cluster-nodetypes.md) nó em um cluster de malha de [serviço](/azure/service-fabric) em execução no Azure é apoiado por um conjunto de escala de máquina virtual. Usando um modelo do Azure Resource Manager, é possível anexar discos de dados para os conjuntos de dimensionamento que compõem o cluster do Service Fabric. É possível usar um [modelo existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como um ponto de partida. No modelo, inclua uma seção _dataDisks_ no _storageProfile_ dos recursos _Microsoft.Compute/virtualMachineScaleSets_ e implante o modelo. O exemplo a seguir anexa um disco de dados de 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

É possível particionar, formatar e montar os discos de dados automaticamente quando o cluster for implantado. Adicione uma extensão de script personalizada para o _extensionProfile_ do _virtualMachineProfile_ dos conjuntos de dimensionamento.

Para preparar os discos de dados automaticamente em um cluster do Windows, adicione o seguinte:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Para preparar os discos de dados automaticamente em um cluster do Linux, adicione o seguinte:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Adicionando discos de dados pré-populados a um conjunto de dimensionamento existente
Os discos de dados especificados no modelo de conjunto de dimensionamento sempre estão vazios. No entanto, você pode anexar um disco de dados existente a uma VM específica em um conjunto de dimensionamento. Se você deseja propagar dados em todas as VMs no conjunto de escalas, você pode duplicar seu disco de dados e anexá-lo a cada VM no conjunto de escalas ou criar uma imagem personalizada que contenha os dados e provisão o conjunto de escala a partir desta imagem personalizada , ou você pode usar arquivos Azure ou uma oferta semelhante de armazenamento de dados.


## <a name="additional-notes"></a>Observações adicionais
O suporte a discos Gerenciados pelo Azure e a discos de dados anexados do conjunto de dimensionamento está disponível na versão [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) ou posterior da API Microsoft.Compute.

O suporte ao portal Azure para discos de dados conectados em conjuntos de escala é limitado. Dependendo dos seus requisitos, você pode usar modelos do Azure, CLI, PowerShell, SDKs e API REST para gerenciar os discos anexados.


