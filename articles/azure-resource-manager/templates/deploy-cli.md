---
title: Implantar recursos com CLI do Azure e modelo
description: Use Azure Resource Manager e CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 8d033bb9ad1c841614ee1e48aa7edc6b8fe18550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372163"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure

Este artigo explica como usar CLI do Azure com modelos de Azure Resource Manager (modelos ARM) para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de suas soluções do Azure, consulte [visão geral da implantação de modelo](overview.md).

Os comandos de implantação foram alterados no CLI do Azure versão 2.2.0. Os exemplos neste artigo exigem CLI do Azure versão 2.2.0 ou posterior.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se você não tiver a CLI do Azure instalada, você pode usar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para um grupo de recursos, uma assinatura, um grupo de gerenciamento ou um locatário. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Para aplicar políticas e atribuições de função em um escopo maior, use assinatura, grupo de gerenciamento ou implantações de locatário. Ao implantar em uma assinatura, você pode criar um grupo de recursos e implantar recursos nele.

Dependendo do escopo da implantação, você usará comandos diferentes.

* Para implantar em um **grupo de recursos**, use [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Para implantar em uma **assinatura**, use [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre as implantações de nível de assinatura, confira [Criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

* Para implantar em um **grupo de gerenciamento**, use [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre implantações de nível de grupo de gerenciamento, confira [Criar recursos no nível de grupo de gerenciamento](deploy-to-management-group.md).

* Para implantar em um **locatário**, use [AZ Deployment locatário Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre implantações de nível de locatário, confira [Criar recursos no nível de locatário](deploy-to-tenant.md).

Os exemplos neste artigo usam implantações de grupo de recursos.

## <a name="deploy-local-template"></a>Implantar o modelo local

Ao implantar recursos no Azure, você:

1. Entre na sua conta do Azure
2. Crie um grupo de recursos que atue como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.
3. Implante no grupo de recursos o modelo que define os recursos a serem criados.

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

## <a name="deployment-name"></a>Nome da implantação

No exemplo anterior, você nomeou a implantação `ExampleDeployment` . Se você não fornecer um nome para a implantação, o nome do arquivo de modelo será usado. Por exemplo, se você implantar um modelo chamado `azuredeploy.json` e não especificar um nome de implantação, a implantação será nomeada `azuredeploy` .

Toda vez que você executa uma implantação, uma entrada é adicionada ao histórico de implantação do grupo de recursos com o nome da implantação. Se você executar outra implantação e fornecer o mesmo nome, a entrada anterior será substituída pela implantação atual. Se você quiser manter entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

Para criar um nome exclusivo, você pode atribuir um número aleatório.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Ou adicione um valor de data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Se você executar implantações simultâneas no mesmo grupo de recursos com o mesmo nome de implantação, somente a última implantação será concluída. Todas as implantações com o mesmo nome que não foram concluídas são substituídas pela última implantação. Por exemplo, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você implanta apenas uma conta de armazenamento. A conta de armazenamento resultante é denominada `storage2` .

No entanto, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e imediatamente após a conclusão da execução de outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento. Um é chamado `storage1` , e o outro é nomeado `storage2` . Mas, você tem apenas uma entrada no histórico de implantação.

Ao especificar um nome exclusivo para cada implantação, você pode executá-los simultaneamente sem conflitos. Se você executar uma implantação chamada `newStorage1` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage2` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento e duas entradas no histórico de implantação.

Para evitar conflitos com implantações simultâneas e para garantir entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos de ARM em seu computador local, você pode preferir armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

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

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Em vez de implantar um modelo local ou remoto, você pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso em sua assinatura do Azure que contém um modelo do ARM. Ele facilita o compartilhamento seguro do modelo com usuários em sua organização. Use o controle de acesso baseado em função do Azure (RBAC do Azure) para conceder acesso à especificação do modelo. Este recurso está atualmente em visualização.

Os exemplos a seguir mostram como criar e implantar uma especificação de modelo. Esses comandos só estarão disponíveis se você tiver se [inscrito na versão prévia](https://aka.ms/templateSpecOnboarding).

Primeiro, você cria a especificação do modelo fornecendo o modelo ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Em seguida, você obtém a ID da especificação do modelo e a implanta.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Para obter mais informações, consulte [Azure Resource Manager de especificações de modelo (versão prévia)](template-specs.md).

## <a name="preview-changes"></a>Visualizar alterações

Antes de implantar seu modelo, você pode visualizar as alterações que o modelo fará no seu ambiente. Use a [operação What-If](template-deploy-what-if.md) para verificar se o modelo faz as alterações que você espera. O What-If também valida o modelo para erros.

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

Se você estiver usando CLI do Azure com prompt de comando do Windows (CMD) ou PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"` .

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

Para passar um objeto, por exemplo, para definir marcas, use JSON. Por exemplo, seu modelo pode incluir um parâmetro como este:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Nesse caso, você pode passar uma cadeia de caracteres JSON para definir o parâmetro, conforme mostrado no seguinte script bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Use aspas duplas em volta do JSON que você deseja passar para o objeto.

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve ser um arquivo local. Não há suporte para arquivos de parâmetro externos com a CLI do Azure.

Para saber mais sobre o arquivo de parâmetro, confira [Criar arquivo de parâmetro do Resource Manager](parameter-files.md).

Para passar um arquivo de parâmetros local, use `@` para especificar um arquivo local chamado storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Manipular o formato JSON estendido

Para implantar um modelo com cadeias de caracteres de várias linhas ou comentários usando CLI do Azure com a versão 2.3.0 ou mais antiga, você deve usar a `--handle-extended-json-format` opção.  Por exemplo:

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

## <a name="next-steps"></a>Próximas etapas

- Para reverter para uma implantação bem-sucedida quando você receber um erro, confira [Reverter em caso de erro para uma implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
- Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
