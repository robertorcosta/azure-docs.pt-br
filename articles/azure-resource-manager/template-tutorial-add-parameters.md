---
title: Tutorial – adicionar parâmetros a um modelo do Azure Resource Manager
description: Adicione parâmetros ao modelo do Azure Resource Manager para torná-lo reutilizável.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f5e631994223d6362512ed0ddc89d1d3c884fbd4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001506"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Tutorial: Adicionar parâmetros ao modelo do Resource Manager

No [tutorial anterior](template-tutorial-add-resource.md), você aprendeu como adicionar uma conta de armazenamento ao modelo e implantá-lo. Neste tutorial, você aprenderá a melhorar o modelo pela adição de parâmetros. Este tutorial demora cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre recursos](template-tutorial-add-resource.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examinar o modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Talvez você tenha notado que há um problema com este modelo. O nome da conta de armazenamento está embutido em código. Você só pode usar esse modelo para implantar a mesma conta de armazenamento a cada vez. Para implantar uma conta de armazenamento com um nome diferente, você precisaria criar um novo modelo, o que obviamente não é uma maneira prática de automatizar suas implantações.

## <a name="make-your-template-reusable"></a>Tornar seu modelo reutilizável

Para tornar seu modelo reutilizável, adicionaremos um parâmetro que você pode usar para passar um nome de conta de armazenamento. O JSON realçado no exemplo a seguir mostra o que mudou no modelo. O parâmetro **storageName** é identificado como uma cadeia de caracteres. O comprimento máximo é definido como 24 caracteres para evitar qualquer nome muito longo.

Copie o arquivo inteiro e substitua o modelo pelo conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-the-template"></a>Implantar o modelo

Vamos implantar o modelo. O exemplo a seguir implanta o modelo com a CLI do Azure ou o PowerShell. Observe que você fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome usado no tutorial anterior.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável **templateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender atualizações de recurso

Na seção anterior, você implantou uma conta de armazenamento com o mesmo nome que você tinha criado anteriormente. Você pode estar se perguntando como o recurso é afetado pela reimplantação.

Se o recurso já existir e nenhuma alteração for detectada nas propriedades, nenhuma ação será executada. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, ele será criado.

Essa maneira de lidar com atualizações significa que seu modelo pode incluir todos os recursos que você precisa para uma solução do Azure. Você pode reimplantar o modelo com segurança e ter a certeza de que os recursos são alterados ou criados somente quando necessário. Por exemplo, se tiver adicionado arquivos à sua conta de armazenamento, você poderá reimplantar a conta de armazenamento sem perder esses arquivos.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem que você personalize a implantação fornecendo valores que são personalizados para determinado ambiente. Por exemplo, você poderá passar valores diferentes dependendo de o ambiente no qual você está implantando ser de desenvolvimento, teste ou produção.

O modelo anterior sempre implantava uma conta de armazenamento Standard_LRS. Talvez você queira a flexibilidade de implantar SKUs diferentes dependendo do ambiente. O exemplo a seguir realça as alterações para adicionar um parâmetro para o SKU. Copie todo o arquivo e cole-o em seu modelo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

O parâmetro **storageSKU** tem um valor padrão. Esse valor é usado quando um valor não é especificado durante a implantação. Ele também tem uma lista de valores permitidos. Esses valores correspondem aos valores necessários para criar uma conta de armazenamento. Você não quer que os usuários do seu modelo transmitam SKUs que não funcionam.

## <a name="redeploy-the-template"></a>Reimplantar o modelo

Você está pronto para implantar novamente. Já que o SKU padrão é definido como **Standard_LRS**, você não precisa fornecer um valor para esse parâmetro.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Para ver a flexibilidade do modelo, vamos implantá-lo novamente. Dessa vez, defina o parâmetro do SKU como **Standard_GRS**. Você pode passar um novo nome para criar uma conta de armazenamento diferente ou usar o mesmo nome para atualizar sua conta de armazenamento existente. As duas opções funcionam.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Por fim, vamos executar mais um teste e ver o que acontece quando você passa um SKU que não é um dos valores permitidos. Nesse caso, testamos o cenário em que um usuário do modelo pensa que **basic** é um dos SKUs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

O comando falha imediatamente com uma mensagem de erro que indica quais valores são permitidos. O Resource Manager identifica o erro antes de iniciar a implantação.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Pela adição de parâmetros, você melhorou o modelo criado no [primeiro tutorial](template-tutorial-create-first-template.md). No próximo tutorial, você aprenderá sobre as funções de modelo.

> [!div class="nextstepaction"]
> [Adicionar funções de modelo](template-tutorial-add-functions.md)
