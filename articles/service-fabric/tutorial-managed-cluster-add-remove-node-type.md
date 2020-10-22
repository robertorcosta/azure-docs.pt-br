---
title: Adicionar e remover os tipos de nós em um cluster gerenciado do Service Fabric (versão prévia)
description: Neste tutorial, saiba como adicionar e remover os tipos de nós de um cluster gerenciado do Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 51cc83b4accae5f2791ce378e30f6fa692446b1c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316204"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Tutorial: Adicionar e remover os tipos de nós de um cluster gerenciado do Service Fabric (versão prévia)

Nesta série de tutoriais, discutiremos:

> [!div class="checklist"]
> * [Como implantar um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Como escalar horizontalmente um cluster gerenciado do Service Fabric](tutorial-managed-cluster-scale.md)
> * Como adicionar e remover nós em um cluster gerenciado do Service Fabric
> * [Como implantar um aplicativo em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy-app.md)

Esta parte da série aborda como:

> [!div class="checklist"]
> * Adicionar um tipo de nó a um cluster gerenciado do Service Fabric
> * Excluir um tipo de nó de um cluster gerenciado do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerenciado do Service Fabric (confira [*Implantar um cluster gerenciado*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) ou posteriores (confira [*Instalar o Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Adicionar um tipo de nó a um cluster gerenciado do Service Fabric

Você pode adicionar um tipo de nó a um cluster gerenciado do Service Fabric por meio de um modelo do Azure Resource Manager, do PowerShell ou da CLI. Neste tutorial, adicionaremos um tipo de nó usando o Azure PowerShell.

Para criar um tipo de nó, você precisará definir três propriedades:

* **Nome do Tipo de Nó**: O nome exclusivo de qualquer tipo de nó existente no cluster.
* **Contagem de Instâncias**: Número inicial de nós do novo tipo de nó.
* **Tamanho da VM**: O SKU da VM para os nós. Se não for especificado, o valor padrão *Standard_D2* será usado.

> [!NOTE]
> Se o tipo de nó que está sendo adicionado for o primeiro ou o único tipo de nó no cluster, a propriedade Primária precisará ser usada.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Remover um tipo de nó de um cluster gerenciado do Service Fabric

Para remover um tipo de nó de um cluster gerenciado do Service Fabric, você precisa usar o PowerShell ou a CLI. Neste tutorial, removeremos um tipo de nó usando o Azure PowerShell.

> [!NOTE]
> Não será possível remover um tipo de nó primário se ele for o único tipo de nó primário no cluster.  

Para remover um tipo de nó:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Próximas etapas

 Nesta seção, adicionamos e excluímos os tipos de nós. Para saber como implantar um aplicativo em um cluster gerenciado do Service Fabric, confira:

> [!div class="nextstepaction"]
> [Implantar um aplicativo em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy-app.md)