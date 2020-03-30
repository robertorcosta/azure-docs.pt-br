---
title: Implantar recursos com a CLI e modelo do Azure
description: Use o Azure Resource Manager e o Azure CLI para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 241b84bc7b8c0b213e74cd7ee5f3d7668fe0d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282640"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Implantar recursos com modelos ARM e Cli do Azure

Este artigo explica como usar o Azure CLI com modelos ARM (Azure Resource Manager) para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de suas soluções Azure, consulte [visão geral da implantação do modelo](overview.md).

Os comandos de implantação foram alterados na versão 2.2.0 do Azure CLI. Os exemplos deste artigo requerem a versão 2.2.0 ou posterior do Azure CLI.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se você não tiver a CLI do Azure instalada, você pode usar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Escopo de implantação

Você pode direcionar sua implantação para um grupo de recursos, assinatura, grupo de gerenciamento ou inquilino. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Para aplicar políticas e atribuições de função em um escopo maior, use assinaturas, grupo de gerenciamento ou implantações de inquilinos. Ao implantar uma assinatura, você pode criar um grupo de recursos e implantar recursos para ele.

Dependendo do escopo da implantação, você usa diferentes comandos.

Para implantar em um **grupo de recursos,** use [a criação de grupo de implantação az](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Para implantar em uma **assinatura,** use [a subcriação de implantação az:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Para obter mais informações sobre implantações no nível de assinatura, consulte [Criar grupos de recursos e recursos no nível de assinatura](deploy-to-subscription.md).

Para implantar em um **grupo de gerenciamento,** use [aaz deployment mg criar](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Para obter mais informações sobre implantações de nível de grupo de gerenciamento, consulte [Criar recursos no nível do grupo de gerenciamento](deploy-to-management-group.md).

Para implantar em um **inquilino,** use [a criação de inquilino de implantação az:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Para obter mais informações sobre implantações de nível de inquilino, consulte [Criar recursos no nível do inquilino](deploy-to-tenant.md).

Os exemplos deste artigo usam implantações de grupo de recursos.

## <a name="deploy-local-template"></a>Implantar o modelo local

Ao implantar recursos no Azure, você:

1. Entre na sua conta do Azure
2. Crie um grupo de recursos que atue como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.
3. Implanta no grupo de recursos o modelo que define os recursos a serem criados

Um modelo pode incluir parâmetros que permitem personalizar a implantação. Por exemplo, você pode fornecer valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para o SKU da conta de armazenamento.

O exemplo a seguir cria um grupo de recursos e implanta um modelo do computador local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos ARM em sua máquina local, você pode preferir armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **template-uri**. Use o URI do exemplo para implantar o modelo de exemplo do GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

No Cloud Shell, use os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parâmetros

Para passar valores de parâmetros, você pode usar parâmetros inline ou um arquivo de parâmetros.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros embutidos, forneça os valores em `parameters`. Por exemplo passar uma cadeia de caracteres e a matriz a um modelo é um shell Bash, use:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se você estiver usando o Azure CLI com o Windows Command Prompt (CMD) ou o PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"`.

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

O formato arrayContent.json é:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve ser um arquivo local. Não há suporte para arquivos de parâmetro externos com a CLI do Azure.

Para obter mais informações sobre o arquivo parâmetro, consulte [Criar arquivo de parâmetros do Gerenciador de recursos](parameter-files.md).

Para passar um arquivo de parâmetros local, use `@` para especificar um arquivo local chamado storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Lidar com o formato JSON estendido

Para implantar um modelo com seqüências ou `--handle-extended-json-format` comentários de várias linhas, você deve usar o switch.  Por exemplo: 

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Testar uma implantação de modelo

Para testar os valores do modelo e dos parâmetros sem realmente implantar recursos, use [a validação do grupo de implantação az](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se nenhum erro for detectado, o comando retornará informações sobre a implantação de teste. Especificamente, observe que o valor de **erro** é null.

```output
{
  "error": null,
  "properties": {
      ...
```

Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, passando um valor incorreto para a SKU da conta de armazenamento, retorna o seguinte erro:

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se seu modelo tem um erro de sintaxe, o comando retorna um erro indicando que ele não foi possível analisar o modelo. A mensagem indica o número da linha e a posição do erro de análise.

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Próximas etapas

- Para reverter para uma implantação bem-sucedida quando tiver um erro, consulte [Reversão de erro para implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [Entenda a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
- Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
- Para distribuir com segurança seu serviço para mais de uma região, consulte [Azure Deployment Manager](deployment-manager-overview.md).
