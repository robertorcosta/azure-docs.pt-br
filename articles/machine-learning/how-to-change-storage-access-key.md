---
title: Alterar as chaves de acesso da conta de armazenamento
titleSuffix: Azure Machine Learning
description: Saiba como alterar as chaves de acesso da conta de armazenamento Do Zure usada pelo seu espaço de trabalho. O Azure Machine Learning usa uma conta do Azure Storage para armazenar dados e modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296937"
---
# <a name="regenerate-storage-account-access-keys"></a>Regenerar chaves de acesso à conta de armazenamento
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como alterar as chaves de acesso para contas de armazenamento do Azure usadas pelo Azure Machine Learning. O Azure Machine Learning pode usar contas de armazenamento para armazenar dados ou modelos treinados.

Para fins de segurança, talvez seja necessário alterar as chaves de acesso de uma conta do Azure Storage. Quando você regenera a chave de acesso, o Azure Machine Learning deve ser atualizado para usar a nova chave. O Azure Machine Learning pode estar usando a conta de armazenamento tanto para armazenamento de modelos quanto como um armazenamento de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o Criar um artigo [sobre o espaço de trabalho.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A [extensão CLI de aprendizado de máquina do Azure](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Os trechos de código neste documento foram testados com a versão 1.0.83 do Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa ser atualizado

As contas de armazenamento podem ser usadas pelo espaço de trabalho Azure Machine Learning (armazenando logs, modelos, snapshots, etc.) e como um armazenamento de dados. O processo de atualização do espaço de trabalho é um único comando Azure CLI e pode ser recorrido após a atualização da chave de armazenamento. O processo de atualização de datastores está mais envolvido e requer descobrir quais datastores estão usando a conta de armazenamento e, em seguida, reregistrá-los.

> [!IMPORTANT]
> Atualize o espaço de trabalho usando o Azure CLI e os datastores usando Python, ao mesmo tempo. Atualizar apenas um ou outro não é suficiente, podendo causar erros até que ambos sejam atualizados.

Para descobrir as contas de armazenamento que são usadas pelos seus datastores, use o seguinte código:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Este código procura quaisquer datastores registrados que utilizem o Azure Storage e lista as seguintes informações:

* Nome do datastore: O nome do datastore em que a conta de armazenamento está registrada.
* Nome da conta de armazenamento: O nome da conta do Azure Storage.
* Contêiner: O recipiente na conta de armazenamento que é usado por este registro.

Ele também indica se o datastore é para um Azure Blob ou um compartilhamento de arquivo Do Zure, pois existem diferentes métodos para reregistrar cada tipo de datastore.

Se existir uma entrada para a conta de armazenamento para a que você planeja regenerar as chaves de acesso, salve o nome do armazenamento de dados, o nome da conta de armazenamento e o nome do contêiner.

## <a name="update-the-access-key"></a>Atualize a chave de acesso

Para atualizar o Azure Machine Learning para usar a nova chave, use as seguintes etapas:

> [!IMPORTANT]
> Execute todas as etapas, atualizando tanto o espaço de trabalho usando o CLI quanto os armazenamentos de dados usando python. Atualizar apenas um ou outro pode causar erros até que ambos sejam atualizados.

1. Regenerar a chave. Para obter informações sobre como regenerar uma chave de acesso, consulte [Gerenciar chaves de acesso à conta de armazenamento](../storage/common/storage-account-keys-manage.md). Guarde a nova chave.

1. Para atualizar o espaço de trabalho para usar a nova chave, use as seguintes etapas:

    1. Para entrar na assinatura do Azure que contém seu espaço de trabalho usando o seguinte comando Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Para atualizar o espaço de trabalho para usar a nova tecla, use o seguinte comando. Substitua pelo `myworkspace` nome do espaço de trabalho `myresourcegroup` azure Machine Learning e substitua pelo nome do grupo de recursos Azure que contém o espaço de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Este comando sincroniza automaticamente as novas chaves para a conta de armazenamento DoZure usada pelo espaço de trabalho.

1. Para recadastrar datastore(s) que usam a conta de armazenamento, use os valores da seção [What needs to ser atualizado](#whattoupdate) e a chave da etapa 1 com o seguinte código:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Uma `overwrite=True` vez especificado, este código substitui o registro existente e atualiza-o para usar a nova chave.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) registro de datastores, consulte a referência da classe.
