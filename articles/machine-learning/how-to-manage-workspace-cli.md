---
title: Crie espaços de trabalho com o Azure CLI
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure CLI para criar um novo espaço de trabalho de Aprendizado de Máquina do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617775"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Crie um espaço de trabalho para aprendizado de máquina do Azure com o Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a criar um espaço de trabalho de Machine Learning do Azure usando o Azure CLI. O Azure CLI fornece comandos para gerenciar os recursos do Azure. A extensão de aprendizado de máquina para a CLI fornece comandos para trabalhar com recursos de Machine Learning do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure.** Se você não tiver um, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos CLI neste documento do ambiente **local,** você precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usa o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acessado através do navegador e vive na nuvem.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à assinatura do Azure

> [!IMPORTANT]
> Se você estiver usando o Azure Cloud Shell, você pode pular esta seção. O shell na nuvem autentica automaticamente você usando a conta que você faz login na sua assinatura do Azure.

Existem várias maneiras que você pode autenticar para a sua assinatura do Azure a partir da CLI. O mais básico é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções [https://aka.ms/devicelogin](https://aka.ms/devicelogin) envolvem navegar e inserir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Para obter outros métodos de autenticação, consulte [Entrar com o Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instale a extensão de aprendizado de máquina

Para instalar a extensão de aprendizado de máquina, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Criar um workspace

O espaço de trabalho azure Machine Learning conta com os seguintes serviços ou entidades do Azure:

> [!IMPORTANT]
> Se você não especificar um serviço Azure existente, um será criado automaticamente durante a criação do espaço de trabalho. Você deve sempre especificar um grupo de recursos.

| Serviço | Parâmetro para especificar uma instância existente |
| ---- | ---- |
| **Grupo de recursos do Azure** | `-g <resource-group-name>`
| **Conta de Armazenamento do Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Cofre de Chave do Azure** | `--keyvault <service-id>` |
| **Registro de Contêiner do Azure** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O espaço de trabalho azure Machine Learning deve ser criado dentro de um grupo de recursos. É possível usar um grupo de recursos existente ou criar um novo. Para __criar um novo grupo de recursos,__ use o seguinte comando. Substitua-o pelo `<resource-group-name>` nome a ser usado para este grupo de recursos. Substitua pela `<location>` região Azure para usar para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região onde o Azure Machine Learning está disponível. Para obter informações, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta deste comando é semelhante ao seguinte JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre como trabalhar com grupos de recursos, consulte [o grupo az](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Criar automaticamente os recursos necessários

Para criar um novo espaço de trabalho onde os __serviços são criados automaticamente,__ use o seguinte comando:

> [!TIP]
> Os comandos nesta seção criam um espaço de trabalho de edição básica. Para criar um espaço de `--sku enterprise` trabalho `az ml workspace create` corporativo, use o switch com o comando. Para obter mais informações sobre as edições do Azure Machine Learning, consulte [O que é Aprendizado de Máquina do Azure](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> O nome do espaço de trabalho é insensível ao caso.

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Usar recursos existentes

Para criar um espaço de trabalho que use os recursos existentes, você deve fornecer o ID para os recursos. Use os seguintes comandos para obter o ID dos serviços:

> [!IMPORTANT]
> Você não precisa especificar todos os recursos existentes. Você pode especificar um ou mais. Por exemplo, você pode especificar uma conta de armazenamento existente e o espaço de trabalho criará os outros recursos.

+ **Conta de armazenamento do Azure**:`az storage account show --name <storage-account-name> --query "id"`

    A resposta deste comando é semelhante ao texto a seguir e é o ID da sua conta de armazenamento:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Insights do aplicativo Azure**:

    1. Instale a extensão de insights do aplicativo:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Obtenha o ID do seu serviço de insights de aplicativos:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A resposta deste comando é semelhante ao texto a seguir e é o ID do seu serviço de insights de aplicativos:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    A resposta deste comando é semelhante ao texto a seguir e é o ID do seu cofre de chaves:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registro de contêineres azure:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A resposta deste comando é semelhante ao texto a seguir, e é o ID do registro de contêiner:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > O registro de contêiner deve ter a conta de [administração](/azure/container-registry/container-registry-authentication#admin-account) ativada antes de ser usada com um espaço de trabalho azure Machine Learning.

Depois de ter os IDs para os recursos que deseja usar com `az workspace create -w <workspace-name> -g <resource-group-name>` o espaço de trabalho, use o comando base e adicione os parâmetros e IDs para os recursos existentes. Por exemplo, o comando a seguir cria um espaço de trabalho que usa um registro de contêiner existente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Listar espaços de trabalho

Para listar todos os espaços de trabalho para sua assinatura do Azure, use o seguinte comando:

```azurecli-interactive
az ml workspace list
```

A saída deste comando é semelhante ao seguinte JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Para obter mais informações, consulte a documentação [da lista de espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Obtenha informações sobre o espaço de trabalho

Para obter informações sobre um espaço de trabalho, use o seguinte comando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para obter mais informações, consulte a documentação do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)

## <a name="update-a-workspace"></a>Atualize um espaço de trabalho

Para atualizar um espaço de trabalho, use o seguinte comando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para obter mais informações, consulte a documentação [de atualização do espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Compartilhe um espaço de trabalho com outro usuário

Para compartilhar um espaço de trabalho com outro usuário em sua assinatura, use o seguinte comando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Para obter mais informações sobre o Controle de Acesso baseado em Funções (RBAC) com o Azure Machine Learning, consulte [Gerenciar usuários e funções](how-to-assign-roles.md).

Para obter mais informações, consulte a documentação [de compartilhamento de espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Sincronizar chaves para recursos dependentes

Se você alterar as chaves de acesso para um dos recursos usados pelo seu espaço de trabalho, use o seguinte comando para sincronizar as novas chaves com o espaço de trabalho:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obter mais informações sobre a alteração de chaves, consulte [Regenerar as teclas de acesso de armazenamento](how-to-change-storage-access-key.md).

Para obter mais informações, consulte a documentação [de sincronização do espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Excluir um workspace

Para excluir um espaço de trabalho depois que ele não for mais necessário, use o seguinte comando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A exclusão de um espaço de trabalho não exclui o insight do aplicativo, a conta de armazenamento, o cofre de chaves ou o registro de contêiner usado pelo espaço de trabalho.

Você também pode excluir o grupo de recursos, que exclui o espaço de trabalho e todos os outros recursos do Azure no grupo de recursos. Para excluir o grupo de recursos, use o seguinte comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para obter mais informações, consulte a documentação de exclusão do [espaço de trabalho az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do espaço de trabalho do Azure Machine Learning para uma assinatura diferente, ou a mudança da assinatura própria para um novo inquilino, não é suportada. Fazer isso pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Excluindo o Registro de Contêineres do Azure

O espaço de trabalho Azure Machine Learning usa o Azure Container Registry (ACR) para algumas operações. Ele criará automaticamente uma instância ACR quando precisar de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a extensão Azure CLI para aprendizado de máquina, consulte a documentação [az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)
