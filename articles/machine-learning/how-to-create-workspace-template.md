---
title: Criar um espaço de trabalho com Azure Resource Manager modelo
titleSuffix: Azure Machine Learning
description: Saiba como usar um modelo de Azure Resource Manager para criar um novo espaço de trabalho Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 568bcdcfd8ae50fff58964ecc74176b151db22a4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121313"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho do Azure Machine Learning usando modelos de Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Além disso, pode especificar os parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo a partir de uma CLI, você precisará do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo do Resource Manager pode ser usado para criar um espaço de trabalho Azure Machine Learning e recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Esse modelo cria os seguintes serviços do Azure:

* Grupo de recursos do Azure
* Conta de Armazenamento do Azure
* Cofre de Chave do Azure
* Azure Application Insights
* Registro de Contêiner do Azure
* Workspace do Azure Machine Learning

O grupo de recursos é o contêiner que retém os serviços. Os diversos serviços são exigidos pelo workspace do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* A **localização** onde o grupo de recursos e serviços serão criados.

    O modelo usará a localização selecionada para a maioria dos recursos. A exceção é o serviço do Application Insights que não está disponível em todos os locais de disponibilidade dos outros serviços. Se você selecionar uma localização onde não esteja disponível, o serviço será criado na localização Centro-Sul dos EUA.

* O **nome do workspace** é o nome amigável do workspace do Azure Machine Learning.

    > [!NOTE]
    > O nome do espaço de trabalho não diferencia maiúsculas de minúsculas.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Embora o modelo associado a este documento crie um novo registro de contêiner do Azure, você também pode criar um novo espaço de trabalho sem criar um registro de contêiner. Uma será criada quando você executar uma operação que requer um registro de contêiner. Por exemplo, treinar ou implantar um modelo.
>
> Você também pode fazer referência a um registro de contêiner ou conta de armazenamento existente no modelo Azure Resource Manager, em vez de criar um novo.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Para obter mais informações sobre modelos, consulte os artigos a seguir:

* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipos de recursos do Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modelo avançado

O modelo de exemplo a seguir demonstra como criar um espaço de trabalho com três configurações:

* Habilitar configurações de alta confidencialidade para o espaço de trabalho
* Habilitar a criptografia para o espaço de trabalho
* Usa um Azure Key Vault existente para recuperar chaves gerenciadas pelo cliente

Para obter mais informações, consulte [criptografia em repouso](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Há alguns requisitos específicos que sua assinatura deve atender antes de usar este modelo:
> * O aplicativo __Azure Machine Learning__ deve ser um __colaborador__ para sua assinatura do Azure.
> * Você deve ter um Azure Key Vault existente que contenha uma chave de criptografia.
> * Você deve ter uma política de acesso no Azure Key Vault que conceda acesso de __Get__, __encapsular__e de __desencapsulamento__ ao aplicativo __Azure Cosmos DB__ .
> * O Azure Key Vault deve estar na mesma região em que você planeja criar o espaço de trabalho Azure Machine Learning.
> * Sua assinatura deve dar suporte a __chaves gerenciadas pelo cliente__ para Azure Cosmos DB.

__Para adicionar o aplicativo Azure Machine Learning como um colaborador__, use os seguintes comandos:

1. Para autenticar no Azure por meio da CLI, use o seguinte comando:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Para obter a ID de objeto do Azure Machine Learning aplicativo, use o comando a seguir. O valor pode ser diferente para cada uma das suas assinaturas do Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Esse comando retorna a ID de objeto, que é um GUID.

1. Para adicionar a ID de objeto como um colaborador à sua assinatura, use o comando a seguir. Substituir `<object-ID>` pelo GUID da etapa anterior. Substitua `<subscription-ID>` pelo nome ou ID da sua assinatura do Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Para adicionar uma chave à sua Azure Key Vault__, use as informações na seção [adicionando uma chave, segredo ou certificado ao cofre de chaves](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) do artigo __gerenciar Key Vault usando CLI do Azure__ .

__Para adicionar uma política de acesso ao cofre de chaves, use os seguintes comandos__:

1. Para obter a ID de objeto do Azure Cosmos DB aplicativo, use o comando a seguir. O valor pode ser diferente para cada uma das suas assinaturas do Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Esse comando retorna a ID de objeto, que é um GUID.

1. Para definir a política, use o comando a seguir. Substituir `<keyvault-name>` pelo nome do Azure Key Vault existente. Substituir `<object-ID>` pelo GUID da etapa anterior:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Para habilitar chaves gerenciadas pelo cliente para Azure Cosmos DB__, envie uma mensagem para azurecosmosdbcmk@service.microsoft.com com sua ID de assinatura do Azure. Para obter mais informações, consulte [Configurar chaves gerenciadas pelo cliente para sua conta do Azure Cosmos](..//cosmos-db/how-to-setup-cmk.md).

__Para obter os valores__ para o `cmk_keyvault` (ID do Key Vault) e os `resource_cmk_uri` parâmetros (URI de chave) necessários para este modelo, use as seguintes etapas:

1. Para obter a ID de Key Vault, use o seguinte comando:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Esse comando retorna um valor semelhante a `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Para obter o valor do URI para a chave gerenciada pelo cliente, use o seguinte comando:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Esse comando retorna um valor semelhante a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Modelo de exemplo__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Depois que um espaço de trabalho tiver sido criado, você não poderá alterar as configurações de dados confidenciais, criptografia, ID do Key Vault ou identificadores de chave. Para alterar esses valores, você deve criar um novo espaço de trabalho usando os novos valores.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Siga as etapas em [Implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Ao acessar a tela __Editar modelo__, cole o modelo deste documento.
1. Selecione __Salvar__ para usar o modelo. Forneça as informações a seguir e concorde com os termos e condições listados:

   * Assinatura: selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do espaço de trabalho: o nome a ser usado para o espaço de trabalho Azure Machine Learning que será criado. O nome do workspace deverá ter entre 3 e 33 caracteres. E o nome poderá conter apenas caracteres alfanuméricos e '-'.
   * Local: selecione o local onde os recursos serão criados.

Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../azure-resource-manager/templates/deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM pode falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.

* Examine as políticas de acesso do Key Vault e use essas políticas para definir a `accessPolicies` Propriedade do modelo. Para exibir as políticas de acesso, use o seguinte comando de CLI do Azure:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais informações sobre como usar a `accessPolicies` seção do modelo, consulte a [referência do objeto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, para usar o Key Vault existente em vez de criar um novo, faça as seguintes alterações no modelo:

    * **Adicione** um parâmetro que aceite a ID de um recurso de Key Vault existente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Remova** a seção que cria um recurso de Key Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Remova** a `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` linha da `dependsOn` seção do espaço de trabalho. **Altere** também a `keyVault` entrada na `properties` seção do espaço de trabalho para fazer referência ao `keyVaultId` parâmetro:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Após essas alterações, você pode especificar a ID do recurso de Key Vault existente ao executar o modelo. Em seguida, o modelo reutilizará a Key Vault definindo a `keyVault` Propriedade do espaço de trabalho como sua ID.

    Para obter a ID do Key Vault, você pode fazer referência à saída da execução do modelo original ou usar o CLI do Azure. O comando a seguir é um exemplo de como usar o CLI do Azure para obter a ID de recurso de Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Esse comando retorna um valor semelhante ao texto a seguir:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Próximas etapas

* [Implante recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
