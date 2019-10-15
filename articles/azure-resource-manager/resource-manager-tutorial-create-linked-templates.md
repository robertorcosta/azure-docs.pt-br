---
title: Criar modelos do Azure Resource Manager vinculados | Microsoft Docs
description: Saiba como criar modelos do Azure Resource Manager vinculados para criar máquinas virtuais
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 869e59aea9b78c44b1a920e58ecefab5e0ca4920
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169405"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutorial: Criar modelos do Azure Resource Manager vinculados

Aprenda a criar modelos do Azure Resource Manager vinculados. Usando modelos vinculados, você pode fazer com que um modelo chame outro. Isso é ótimo para modularizar modelos. Neste tutorial, você usa o mesmo modelo usado no [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md), que cria uma máquina virtual, uma rede virtual e outros recursos dependentes, incluindo uma conta de armazenamento. Você separa a criação do recurso de conta de armazenamento para um modelo vinculado.

Chamar um modelo vinculado é como fazer uma chamada de função.  Você também aprenderá como passar valores de parâmetros para o modelo vinculado e como obter "valores retornados" do modelo vinculado.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Criar o modelo vinculado
> * Carregar o modelo vinculado
> * Link para o modelo vinculado
> * Configurar dependência
> * Implantar o modelo
> * Práticas adicionais

Para saber mais, consulte [Usar modelos vinculados e aninhados ao implantar recursos do Azure](./resource-group-linked-templates.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Este é o mesmo modelo usado em [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Salve duas cópias do mesmo modelo a ser usado como:

* **O modelo principal**: crie todos os recursos exceto a conta de armazenamento.
* **O modelo vinculado**: crie a conta de armazenamento.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Há cinco recursos definidos pelo modelo:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
5. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.
6. Selecione **Arquivo**>**Salvar como** para criar outra cópia do arquivo com o nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Criar o modelo vinculado

O modelo vinculado cria uma conta de armazenamento. O modelo vinculado pode ser usado como um modelo autônomo para criar uma conta de armazenamento. Neste tutorial, o modelo vinculado usa dois parâmetros e precisa retornar um valor ao modelo principal. Esse valor de "retorno" é definido no elemento `outputs`.

1. Abra **linkedTemplate.json** no Visual Studio Code, se o arquivo não estiver aberto.
2. Faça as seguintes alterações:

    * Remova todos os parâmetros que não sejam **localização**.
    * Adicione um parâmetro chamado **storageAccountName**.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        O nome e a localização da conta de armazenamento são transmitidos do modelo principal para o modelo vinculado como parâmetros.

    * Remova o elemento **variáveis** e todas as definições de variável.
    * Remova todos os recursos que não sejam a conta de armazenamento. Você removerá, no total, quatro recursos.
    * Atualize o valor do elemento **nome** do recurso de conta de armazenamento para:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Atualize o elemento **outputs** para que ele se pareça com:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       **storageUri** é exigido pela definição de recurso de máquina virtual no modelo principal.  Retorne o valor para o modelo principal como um valor de saída.

        Quando você terminar, o modelo deverá ser semelhante a:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. Salve as alterações.

## <a name="upload-the-linked-template"></a>Carregar o modelo vinculado

O modelo principal e o modelo vinculado precisam estar acessíveis do local em que você executa a implantação. Neste tutorial, você deve usar o método de implantação do Cloud Shell usado no [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md). O modelo principal (azuredeploy.json) é carregado no shell. O modelo vinculado (linkedTemplate.json) deve ser compartilhado em algum lugar com segurança. O seguinte script do PowerShell cria uma conta de Armazenamento do Azure, carrega o modelo para a conta de Armazenamento e, em seguida, gera um token SAS para permitir acesso limitado geral ao arquivo de modelo. Para simplificar o tutorial, o script baixa um modelo vinculado concluído de um repositório GitHub. Se você quiser usar o modelo vinculado é criado, poderá usar o [Cloud Shell](https://shell.azure.com) para carregar o modelo vinculado e, em seguida, modificar o script para usar seu próprio modelo vinculado.

> [!NOTE]
> O script limita o token SAS a ser usado dentro de oito horas. Se você precisar de mais tempo para concluir este tutorial, aumente o tempo de expiração.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Selecione o botão verde **Experimentar** para abrir o painel do Azure Cloud Shell.
2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse em qualquer lugar dentro do painel de shell (a parte azul marinho azul) e, em seguida, selecione **Colar**.
4. Anote os dois valores (Nome do Grupo de Recursos e URI do modelo vinculado) no final do painel do shell. Mais tarde, você precisará desses valores neste tutorial.
5. Selecione **Sair do modo de foco** para fechar o painel do shell.

Na prática, você gera um token SAS quando implanta o modelo principal e dá ao término do token SAS uma janela menor para torná-lo mais seguro. Para obter mais informações, confira [Fornecer token de SAS durante a implantação](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Chamar o modelo vinculado

O modelo principal se chama azuredeploy.json.

1. Abra **azuredeploy.json** no Visual Studio Code, se não estiver aberto.
2. Exclua a definição de recurso de conta de armazenamento do modelo:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Adicione o seguinte snippet de json ao local em que você tinha a definição da conta de armazenamento:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Preste atenção a estes detalhes:

    * Um recurso `Microsoft.Resources/deployments` no modelo principal é usado para vincular a outro modelo.
    * O recurso `deployments` se chama `linkedTemplate`. Esse nome é usado para [configurar a dependência](#configure-dependency).
    * Você só pode usar o modo de implantação [Incremental](./deployment-modes.md) quando chama modelos vinculados.
    * `templateLink/uri` contém o URI do modelo vinculado. Atualize o valor para o URI que você obtém ao carregar o modelo vinculado (aquele com um token SAS).
    * Use `parameters` para passar valores do modelo principal para o modelo vinculado.
4. Verifique se você atualizou o valor do elemento `uri` para o valor que você obteve ao carregar o modelo vinculado (aquele com um token SAS). Na prática, se você quiser fornecer ao URI um parâmetro.
5. Salve o modelo revisado

## <a name="configure-dependency"></a>Configurar dependência

Lembre-se do [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md), o recurso de máquina virtual depende da conta de armazenamento:

![Diagrama de dependência dos modelos do Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Como a conta de armazenamento agora está definida no modelo vinculado, você deve atualizar os dois elementos a seguir do recurso `Microsoft.Compute/virtualMachines`.

* Reconfigure o elemento `dependsOn`. A definição da conta de armazenamento é movida para o modelo vinculado.
* Reconfigure o elemento `properties/diagnosticsProfile/bootDiagnostics/storageUri`. Em [Criar o modelo vinculado](#create-the-linked-template), você adicionou um valor de saída:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Esse valor é exigido pelo modelo principal.

1. Abra o azuredeploy.json no Visual Studio Code se já não estiver aberto.
2. Expanda a definição de recurso de máquina virtual e atualize **dependsOn** conforme mostrado na seguinte captura de tela:

    ![Os modelos vinculados do Azure Resource Manager configuram a dependência](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* é o nome do recurso de implantações.
3. Atualize **properties/diagnosticsProfile/bootDiagnostics/storageUri/** conforme mostrado na captura de tela anterior.
4. Salve o modelo revisado.

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implantação. Use o mesmo nome de grupo de recursos que a conta de armazenamento para armazenar o modelo vinculado. Isso torna mais fácil limpar recursos na próxima seção. Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Consulte [Pré-requisitos](#prerequisites).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="additional-practice"></a>Prática adicional

Para melhorar o projeto, faça as seguintes alterações adicionais ao projeto concluído:

1. Modifique o modelo principal (azuredeploy.json) para que ele use o valor do URI do modelo vinculado por meio de um parâmetro.
2. Em vez de gerar um token SAS ao carregar o modelo vinculado, gere o token quando você implanta o modelo principal. Para obter mais informações, confira [Fornecer token de SAS durante a implantação](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você modularizou um modelo em um modelo principal e um modelo vinculado. Para saber como usar extensões de máquina virtual para executar tarefas de pós-implantação, confira:

> [!div class="nextstepaction"]
> [Implantar extensões de máquina virtual](./resource-manager-tutorial-deploy-vm-extensions.md)
