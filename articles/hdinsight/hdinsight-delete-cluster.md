---
title: Como excluir um cluster HDInsight - Azure
description: Informações sobre as várias maneiras que você pode excluir um cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807129"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure

A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. O faturamento é proporcional por minuto, então você deve sempre excluir seu cluster quando ele não estiver mais em uso. Neste documento, você aprende a excluir um cluster usando o [portal Azure](https://portal.azure.com), [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)e o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> A exclusão de um cluster HDInsight não exclui as contas do Armazenamento do Azure ou Data Lake Storage associadas ao cluster. Você pode reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com).

2. No menu à esquerda, navegue até **todos os serviços** > **do Analytics** > **HDInsight e** selecione seu cluster.

3. Na exibição padrão, selecione o ícone **Excluir.** Siga o prompt para excluir seu cluster.

    ![Botão de cluster de exclusão do HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Substitua pelo `CLUSTERNAME` nome do seu cluster HDInsight no código abaixo. A partir de um prompt PowerShell, digite o seguinte comando para excluir o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

Substitua pelo `CLUSTERNAME` nome do seu `RESOURCEGROUP` cluster HDInsight e pelo nome do seu grupo de recursos no código abaixo.  A partir de um prompt de comando, digite o seguinte para excluir o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
