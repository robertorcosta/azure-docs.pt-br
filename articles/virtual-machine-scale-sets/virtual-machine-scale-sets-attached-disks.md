---
title: Discos de dados anexados de conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como usar discos de dados anexados com conjuntos de dimensionamento de máquinas virtuais por meio de contornos de casos de uso específicos.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: c7fd4d89fcc66fb4110029be45ad94e21faea0e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386644"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Discos de dados conectados e conjuntos de dimensionamento de máquina virtual do Azure
Para expandir o armazenamento disponível, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure oferecem suporte a instâncias de VM com discos de dados anexados. É possível anexar discos de dados ao criar o conjunto de dimensionamento ou em um já existente.

> [!NOTE]
> Ao criar um conjunto de dimensionamento com discos de dados anexado, você precisa montar e formatar os discos de dentro de uma VM para usá-los (assim como para VMs autônomas do Azure). Uma maneira conveniente de concluir esse processo é usar uma Extensão de script personalizado que chama um script para particionar e formatar todos os discos de dados em uma VM. Para obter exemplos disso, consulte [CLI do Azure](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Criar e gerenciar discos em um conjunto de dimensionamento
Para obter informações detalhadas sobre como criar um conjunto de dimensionamento com discos de dados anexado, preparar e formatar ou adicionar e remover discos de dados, consulte um dos seguintes tutoriais:

- [CLI do Azure](tutorial-use-disks-cli.md)
- [PowerShell do Azure](tutorial-use-disks-powershell.md)

O restante deste artigo descreve os casos de uso específico, como clusters do Service Fabric que requerem discos de dados ou anexar discos de dados existente com conteúdo para um conjunto de dimensionamento.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Criar um cluster do Service Fabric com discos de dados anexados
Cada [tipo de nó](../service-fabric/service-fabric-cluster-nodetypes.md) em um cluster do [Service Fabric](/azure/service-fabric) sendo executado no Azure é respaldado por um conjunto de dimensionamento de máquinas virtuais. Usando um modelo do Azure Resource Manager, é possível anexar discos de dados para os conjuntos de dimensionamento que compõem o cluster do Service Fabric. É possível usar um [modelo existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como um ponto de partida. No modelo, inclua uma seção _dataDisks_ no _storageProfile_ dos recursos _Microsoft.Compute/virtualMachineScaleSets_ e implante o modelo. O exemplo a seguir anexa um disco de dados de 128 GB:

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
Os discos de dados especificados no modelo de conjunto de dimensionamento sempre estão vazios. No entanto, você pode anexar um disco de dados existente a uma VM específica em um conjunto de dimensionamento. Esse recurso está em versão prévia, com exemplos no [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Se desejar propagar os dados para todas as VMs no conjunto de dimensionamento, você poderá duplicar o disco de dados e anexá-lo a cada VM do conjunto de dimensionamento, criar uma imagem personalizada que contém os dados e provisionar o conjunto de dimensionamento com base nessa imagem personalizada ou usar os Arquivos do Azure ou uma oferta de armazenamento de dados semelhante.


## <a name="additional-notes"></a>Observações adicionais
O suporte a discos Gerenciados pelo Azure e a discos de dados anexados do conjunto de dimensionamento está disponível na versão [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) ou posterior da API Microsoft.Compute.

Suporte do portal do Azure para discos de dados anexados em conjuntos de escala é limitada inicialmente. Dependendo dos seus requisitos, você pode usar modelos do Azure, CLI, PowerShell, SDKs e API REST para gerenciar os discos anexados.


