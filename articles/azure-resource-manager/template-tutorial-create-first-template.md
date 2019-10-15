---
title: Tutorial – Criar e implantar seu primeiro modelo do Azure Resource Manager
description: Descreve as etapas para criar seu primeiro modelo do Azure Resource Manager. Você aprende sobre a sintaxe do arquivo de modelo e como implantar uma conta de armazenamento.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e84c3bfa5a5f43ec652f12d70718ab63bd7a19e5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029542"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Tutorial: Criar e implantar seu primeiro modelo do Azure Resource Manager

Este tutorial apresenta os modelos do Resource Manager. Ele mostra como criar um modelo inicial e implantá-lo no Azure. Você aprenderá sobre a estrutura do modelo e as ferramentas necessárias para trabalhar com modelos. Leva cerca de **12 minutos** para concluir este tutorial, mas o tempo real varia com base em quantas ferramentas você precisa instalar.

Este tutorial é o primeiro de uma série. À medida que avança pela série, você modifica o modelo inicial passo a passo até explorar todas as partes principais de um modelo do Resource Manager. Esses elementos são os blocos de construção para modelos muito mais complexos. Esperamos que ao final da série você tenha confiança para criar seus próprios modelos e esteja pronto para automatizar suas implantações com modelos.

Se você quiser saber mais sobre os benefícios de usar modelos e por que deve automatizar a implantação com modelos, confira [modelos do Azure Resource Manager](template-deployment-overview.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="get-tools"></a>Obter ferramentas

Vamos começar verificando se você tem as ferramentas necessárias para criar e implantar modelos.

### <a name="editor"></a>Editor

Modelos são arquivos JSON. Para criar modelos, você precisa de um bom editor de JSON. Recomendamos o Visual Studio Code com a extensão Ferramentas do Resource Manager. Se você precisar instalar essas ferramentas, confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Implantação de linha de comando

Você também precisa do Azure PowerShell ou da CLI do Azure para implantar o modelo. Para obter as instruções de instalação, confira:

- [Instale o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instalar a CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)

Depois de instalar o Azure PowerShell ou a CLI do Azure, é preciso que você entre pela primeira vez. Para obter ajuda, confira [Entrar – PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Entrar – CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Ok, você está pronto para começar a aprender sobre modelos.

## <a name="create-your-first-template"></a>Criar seu primeiro modelo

1. Abra o Visual Studio Code com a extensão Ferramentas do Resource Manager instalada.
1. Do menu **Arquivo**, selecione **Novo Arquivo** para criar um novo arquivo.
1. No menu **Arquivo**, selecione **Salvar como**.
1. Nomeie o arquivo **azuredeploy** e selecione a extensão de arquivo **JSON**. O nome completo do arquivo **azuredeploy.json**.
1. Salve o arquivo na estação de trabalho. Selecione um caminho que seja fácil de lembrar, pois você fornecerá esse caminho mais tarde ao implantar o modelo.
1. Copie e cole o JSON a seguir no arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Esta é a aparência do seu ambiente do VS Code:

    ![Primeiro modelo do Visual Studio Code do modelo do Resource Manager](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Este modelo não implanta nenhum recurso. Estamos começando com um modelo em branco para que você possa se familiarizar com as etapas para implantar um modelo, minimizando a chance de algo dar errado.

    O arquivo JSON tem estes elementos:

    - **$schema**: especifica a localização do arquivo de esquema JSON. O arquivo de esquema descreve as propriedades que estão disponíveis em um modelo. Por exemplo, o esquema define **resources** como uma das propriedades válidas para um modelo. Não se preocupe se a data do esquema é 01/01/2015. Essa versão de esquema está atualizada e inclui todos os recursos mais recentes. A data do esquema não foi alterada porque não houve alterações significativas desde sua introdução.
    - **contentVersion**: Especifica a versão do modelo (por exemplo, 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado.
    - **resources**: Contém os recursos que você deseja implantar ou atualizar. Atualmente está vazia, mas você adicionará recursos mais tarde.

1. Salve o arquivo.

Parabéns, você criou seu primeiro modelo.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para começar a trabalhar com o Azure PowerShell/CLI do Azure, entre com suas credenciais do Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implantar um modelo, você especificará um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell. Selecione as guias na seção de código a seguir para escolher entre o Azure PowerShell e a CLI do Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implantar modelo

Para implantar o modelo, use a CLI do Azure ou o Azure PowerShell. Use o grupo de recursos que você criou anteriormente. Dê um nome para a implantação para que você possa identificá-la facilmente no histórico de implantação. Para sua conveniência, crie também uma variável que armazena o caminho para o arquivo de modelo. Essa variável facilita a execução dos comandos de implantação porque você não precisa digitar o caminho sempre que implantar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se a implantação foi bem-sucedida.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![Estado de provisionamento de implantação do PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

![Estado de provisionamento de implantação da CLI do Azure](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **Grupos de recursos**.

1. Selecione o grupo de recursos implantado no último procedimento. O nome padrão é **myResourceGroup**. Você não deverá ver nenhum recurso implantado dentro do grupo de recursos.

1. Observe que, no canto superior direito da visão geral, o status da implantação é exibido. Selecione **1 Bem-sucedida**.

   ![Exibir status da implantação](./media/template-tutorial-create-first-template/deployment-status.png)

1. Você vê um histórico das implantações do grupo de recursos. Selecione **blanktemplate**.

   ![Selecionar a implantação](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Você verá um resumo da implantação. Nesse caso não há muito o que ver, porque nenhum recurso foi implantado. Posteriormente nesta série, talvez você ache útil examinar o resumo no histórico de implantação. Observe que à esquerda você pode exibir entradas, saídas e o modelo usado durante a implantação.

   ![Exibir resumo da implantação](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira excluir o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você criou um modelo simples para implantar no Azure. No próximo tutorial, você adicionará uma conta de armazenamento ao modelo e a implantará em seu grupo de recursos.

> [!div class="nextstepaction"]
> [Adicionar recurso](template-tutorial-add-resource.md)
