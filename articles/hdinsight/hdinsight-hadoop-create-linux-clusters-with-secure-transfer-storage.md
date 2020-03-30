---
title: Apache Hadoop & armazenamento seguro de transferência - Azure HDInsight
description: Saiba como criar clusters HDInsight com contas de armazenamento do Azure com transferência segura habilitada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560075"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Clusters Apache Hadoop com contas seguras de armazenamento de transferência no Azure HDInsight

O recurso [Transferência segura exigida](../storage/common/storage-require-secure-transfer.md) aprimora a segurança de sua conta de Armazenamento do Azure aplicando todas as solicitações à sua conta por meio de uma conexão segura. Esse recurso e o esquema wasbs só têm suporte da versão 3.6 ou mais recente do cluster HDInsight.

> [!IMPORTANT]
> Permitir a transferência segura de armazenamento após a criação de um cluster pode resultar em erros usando sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com transferência segura já ativada.

## <a name="storage-accounts"></a>Contas de armazenamento

### <a name="azure-portal"></a>Portal do Azure

Por padrão, a propriedade de transferência segura necessária é ativada quando você cria uma conta de armazenamento no portal Azure.

Para atualizar uma conta de armazenamento existente com o portal Azure, consulte [Exigir transferência segura com o portal Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Para o PowerShell cmdlet [New-AzStorageAccount , certifique-se](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)de que o parâmetro `-EnableHttpsTrafficOnly` está definido como `1`.

Para atualizar uma conta de armazenamento existente com o PowerShell, consulte [Exigir transferência segura com o PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>CLI do Azure

Para criar a [conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)do `--https-only` comando `true`Azure CLI, certifique-se de que o parâmetro está definido como .

Para atualizar uma conta de armazenamento existente com o Azure CLI, consulte [Exigir transferência segura com o Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Há várias opções para adicionar outras contas de armazenamento com transferência segura habilitada:

* Modifique o modelo do Azure Resource Manager na última seção.
* Crie um cluster usando o [Portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento vinculada.
* Use ações de script para adicionar outras contas de armazenamento com transferência segura habilitada a um cluster existente do HDInsight. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximas etapas

* O uso do Armazenamento do Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como armazenamento de dados padrão
* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight](hdinsight-upload-data.md).
