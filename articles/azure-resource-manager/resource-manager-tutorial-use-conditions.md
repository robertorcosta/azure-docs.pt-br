---
title: Condição de uso em modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como implantar recursos do Azure com base em condições. Mostra como implantar um novo recurso ou usar um recurso existente.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5fa1db5131f555c92aa12fc01a109469c2cf819e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390185"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Tutorial: Condição de uso em modelos do Azure Resource Manager

Saiba como implantar recursos do Azure com base em condições.

No tutorial [Definir ordem de implantação de recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), você criará uma máquina virtual, uma rede virtual e alguns outros recursos dependentes, incluindo uma conta de armazenamento. Em vez de criar uma nova conta de armazenamento todas as vezes, você permitirá que as pessoas escolham entre criar uma nova conta de armazenamento e usar uma conta de armazenamento existente. Para isso, você definirá um parâmetro adicional. Se o valor do parâmetro for “new”, uma nova conta de armazenamento será criada. Caso contrário, uma conta de armazenamento existente com o nome fornecido é usada.

![Diagrama de condição de uso do modelo do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Modificar o modelo
> * Implantar o modelo
> * Limpar recursos

Este tutorial aborda apenas um cenário básico de como usar as condições. Para obter mais informações, consulte:

* [Estrutura de arquivos de modelo: Condição](conditional-resource-deployment.md).
* [Implantar condicionalmente um recurso em um modelo do Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Função de modelo: If](./resource-group-template-functions-logical.md#if).
* [Funções de comparação para modelos do Azure Resource Manager](./resource-group-template-functions-comparison.md)

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.
4. Há cinco recursos definidos pelo modelo:

   * `Microsoft.Storage/storageAccounts`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
5. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="modify-the-template"></a>Modificar o modelo

Faça duas alterações no modelo existente:

* Adicione um parâmetro de nome da conta de armazenamento. Os usuários podem especificar um nome de conta de armazenamento novo ou existente.
* Adicione um novo parâmetro chamado **newOrExisting**. A implantação usa esse parâmetro para determinar se uma nova conta de armazenamento será criada ou uma conta de armazenamento existente será usada.

Aqui está o procedimento para fazer as alterações:

1. Abra **azuredeploy.json** no Visual Studio Code.
2. Substitua as três **variables('storageAccountName')** por **parameters('storageAccountName')** em todo o modelo.
3. Remova as declarações de variável a seguir:

    ![Diagrama de condição de uso do modelo do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Adicione os dois parâmetros a seguir ao modelo:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    A definição dos parâmetros atualizados ficará assim:

    ![Condição de uso do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Adicione a seguinte linha no início da definição da conta de armazenamento.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A condição verifica o valor de um parâmetro chamado **newOrExisting**. Se o valor do parâmetro for **new**, a implantação criará a conta de armazenamento.

    A definição da conta de armazenamento atualizada será assim:

    ![Condição de uso do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Atualize a propriedade **storageUri** da definição de recurso de máquina virtual pelo seguinte valor:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Essa alteração é necessária quando você usa uma conta de armazenamento existente em um grupo de recursos diferentes.

7. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

Siga as instruções em [Implementar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para abrir o Cloud Shell e carregar o modelo revisado e, em seguida, execute o script do PowerShell a seguir para implantar o modelo.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> A implantação falhará se **newOrExisting** for **new**, mas a conta de armazenamento com o nome da conta de armazenamento especificado já existir.

Tente criar outra implantação com **newOrExisting** definido como “existing” e especifique uma conta de armazenamento existente. Para criar uma conta de armazenamento com antecedência, confira [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Para excluir o grupo de recursos, selecione **Testar** para abrir o Cloud Shell. Para colar o script do PowerShell, clique com o botão direito do mouse no painel do shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolveu um modelo que permite aos usuários escolher entre criar uma nova conta de armazenamento e usar uma conta de armazenamento existente. Para saber como recuperar segredos do Azure Key Vault e usá-los como senhas na implantação de modelo, confira:

> [!div class="nextstepaction"]
> [Integrar o Key Vault à implantação de modelo](./resource-manager-tutorial-use-key-vault.md)
