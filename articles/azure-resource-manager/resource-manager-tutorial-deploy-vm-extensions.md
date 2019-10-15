---
title: Implantar extensões de máquina virtual do Azure com modelos do Azure Resource Manager | Microsoft Docs
description: Aprenda a implantar extensões de máquina virtual do Azure com modelos do Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 338054aadbf04c6c6e2b496677476c2c5634b6ba
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169293"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager

Saiba como usar [extensões de máquina virtual do Azure](../virtual-machines/extensions/features-windows.md) para executar tarefas de configuração e automação pós-implantação em VMs do Azure. Muitas extensões de VM diferentes estão disponíveis para uso com as VMs do Azure. Neste tutorial, você implantará uma extensão de Script Personalizado de um modelo do Azure Resource Manager para executar um script do PowerShell em uma VM Windows.  O script instala o servidor Web na VM.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um script do PowerShell
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager. Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-powershell-script"></a>Preparar um script do PowerShell

Um script do PowerShell com o seguinte conteúdo é compartilhado do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o elemento `fileUri` no modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
1. Na caixa **Nome do arquivo**, cole a URL a seguir: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Para abrir o arquivo, selecione **Abrir**.
    O modelo define cinco recursos:

   * **Microsoft.Storage/storageAccounts**. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.

1. Salve uma cópia do arquivo em seu computador local com o nome *azuredeploy.json* selecionando **Arquivo** > **Salvar como**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Para obter mais informações sobre essa definição de recurso, veja [referência de extensão](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Abaixo estão alguns elementos importantes:

* **nome**: como o recurso de extensão é um recurso filho do objeto de máquina virtual, o nome deve ter o prefixo do nome da máquina virtual. Confira [Definir o nome e o tipo de recursos filho](child-resource-name-type.md).
* **dependsOn**: Crie o recurso de extensão depois de criar a máquina virtual.
* **fileUris**: os locais em que os arquivos de script são armazenados. Se você optar por não usar o local fornecido, precisará atualizar os valores.
* **commandToExecute**: esse comando chama o script.

## <a name="deploy-the-template"></a>Implantar o modelo

Para obter o procedimento de implantação, veja a seção "Implantar o modelo" do [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). É recomendável usar uma senha gerada para a conta de administrador da máquina virtual. Veja a seção [Pré-requisitos](#prerequisites) deste artigo.

## <a name="verify-the-deployment"></a>Verificar a implantação

1. No portal do Azure, selecione a VM.
1. Na visão geral da VM, copie o endereço IP selecionando **Clique para copiar** e, em seguida, cole-o em uma guia do navegador. A página de boas-vindas do IIS (Serviços de Informações da Internet) padrão é aberta:

![A página de boas-vindas dos Serviços de Informações da Internet](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisa mais dos recursos do Azure que implantou, limpe-os exclui o grupo de recursos.

1. No portal do Azure, no painel esquerdo, selecione **Grupo de recursos**.
2. Na caixa **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
    Seis recursos serão exibidos no grupo de recursos.
4. No menu do grupo, selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou uma máquina virtual e uma extensão da máquina virtual. A extensão instalou o servidor Web do IIS na máquina virtual. Para saber como usar a extensão do Banco de Dados SQL do Azure para importar um arquivo BACPAC, confira:

> [!div class="nextstepaction"]
> [Implantar extensões de SQL](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
