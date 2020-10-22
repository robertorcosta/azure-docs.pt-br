---
title: Escalar horizontalmente um cluster gerenciado do Service Fabric (versão prévia)
description: Neste tutorial, saiba como escalar horizontalmente um tipo de nó de um cluster gerenciado do Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 01b299744d462496296884211eff08b7a9c64687
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316131"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Tutorial: Escalar horizontalmente um cluster gerenciado do Service Fabric (versão prévia)

Nesta série de tutoriais, discutiremos:

> [!div class="checklist"]
> * [Como implantar um cluster gerenciado do Service Fabric.](tutorial-managed-cluster-deploy.md)
> * Como escalar horizontalmente um cluster gerenciado do Service Fabric
> * [Como adicionar e remover os tipos de nós em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Como implantar um aplicativo em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy-app.md)

Esta parte da série aborda como:

> [!div class="checklist"]
> * Escalar um nó de cluster gerenciado do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerenciado do Service Fabric (confira [*Implantar um cluster gerenciado*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) ou posteriores (confira [*Instalar o Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Escalar um cluster gerenciado do Service Fabric
Altere a contagem de instâncias para aumentar ou diminuir o número de nós no tipo de nó que você deseja escalar. Você pode localizar nomes de tipo de nó no modelo do ARM (Azure Resource Manager) da sua implantação de cluster ou no Service Fabric Explorer.  

> [!NOTE]
> Se o tipo de nó for primário, não será possível ter menos de três nós para um cluster de SKU Básico e cinco nós para um cluster de SKU Standard.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

O cluster começará a ser atualizado automaticamente e, após alguns minutos, você verá os nós adicionais.

## <a name="next-steps"></a>Próximas etapas

Nesta etapa, escalamos um tipo de nó em um cluster gerenciado do Service Fabric. Para saber mais sobre como adicionar e remover os tipos de nós, confira:

> [!div class="nextstepaction"]
> [Adicionar e remover os tipos de nós em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)