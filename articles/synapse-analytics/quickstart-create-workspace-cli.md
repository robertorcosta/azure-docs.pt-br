---
title: 'Início Rápido: Criar um workspace do Synapse usando a CLI do Azure'
description: Crie um workspace do Azure Synapse usando a CLI do Azure seguindo as etapas neste guia.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 065e24c12e0750cd7a1f3ce2d4cad5c7ad4b95df
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260675"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Início Rápido: Criar um workspace do Azure Synapse com a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando.

Neste guia de início rápido, você aprenderá a criar um workspace do Synapse usando a CLI do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Baixar e instalar o [jq](https://stedolan.github.io/jq/download/), um processador JSON de linha de comando leve e flexível
- [Conta de armazenamento do Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > O workspace do Azure Synapse precisa conseguir fazer leituras e gravações na conta do ADLS Gen2 selecionada. Além disso, para qualquer conta de armazenamento vinculada como a conta de armazenamento primária, você precisa ter habilitado **namespace hierárquico** na criação da conta de armazenamento, conforme descrito na página [Criar uma Conta de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instalar a CLI do Azure localmente

Se você optar por instalar e usar a CLI do Azure localmente, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Se você estiver executando a CLI do Azure localmente, precisará fazer logon e se autenticar. Esta etapa não será necessária se você estiver usando o Azure Cloud Shell. Para fazer logon na CLI do Azure, execute `az login` e autentique-se na janela do navegador:

```azurecli
az login
```

Para obter mais informações sobre a autenticação com a CLI do Azure, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Instalar a extensão do Azure Synapse para a CLI do Azure

```azurecli
az extension add --name synapse
```

> [!WARNING]
> A extensão do Azure Synapse para CLI do Azure está em versão prévia.

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Criar um workspace do Azure Synapse usando a CLI do Azure

1. Defina as variáveis de ambiente necessárias para criar recursos para o workspace do Azure Synapse.

    | Nome de variável de ambiente | Descrição |
    |---|---|---|
    |StorageAccountName| Nome de sua conta de armazenamento existente do ADLS Gen2.|
    |StorageAccountResourceGroup| Nome do seu grupo de recursos da conta de armazenamento do ADLS Gen2. |
    |FileShareName| Nome do sistema de arquivos de armazenamento existente.|
    |SynapseResourceGroup| Escolha um novo nome para o grupo de recursos do Azure Synapse. |
    |Região| Escolha uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Escolha um nome exclusivo para seu novo workspace do Azure Synapse. |
    |SqlUser| Escolha um valor para um novo nome de usuário.|
    |SqlPassword| Escolha uma senha segura.|
    |||

2. Crie um grupo de recursos como um contêiner para seu workspace do Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Recupere a chave da conta do Armazenamento do ADLS Gen 2:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Recupere a URL do ponto de extremidade do Armazenamento do ADLS Gen 2:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (Opcional) Você sempre pode verificar quais são a chave e o ponto de extremidade da conta do Armazenamento do ADLS Gen2:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Crie um workspace do Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Obtenha a URL da Web e de desenvolvimento para o workspace do Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Crie uma regra de firewall para permitir o acesso ao workspace do Azure Synapse em seu computador:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Abra o endereço URL da Web do workspace do Azure Synapse armazenado na variável de ambiente `WorkspaceWeb` para acessar seu workspace:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Web do workspace do Azure Synapse](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Limpar os recursos

Siga as etapas abaixo para excluir o workspace do Azure Synapse.
> [!WARNING]
> A exclusão de um workspace do Azure Synapse removerá os mecanismos de análise e os dados armazenados no banco de dados dos metadados do workspace e dos pools de SQL independentes. Não será mais possível se conectar aos pontos de extremidade do SQL ou do Apache Spark. Todos os artefatos de código serão excluídos (consultas, notebooks, definições de trabalho e pipelines).
>
> A exclusão do workspace **não** afetará os dados do Data Lake Storage Gen2 vinculados ao workspace.

Caso deseje excluir o workspace do Azure Synapse, conclua o seguinte comando:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá [criar pools de SQL](quickstart-create-sql-pool-studio.md) ou [criar pools do Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar seus dados.
