---
title: Teste o arquivo de definição de interface do ui
description: Descreve como testar a experiência do usuário para criar o Aplicativo Gerenciado do Azure por meio do portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250190"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Teste sua interface de portal para aplicativos gerenciados do Azure

Depois [de criar o arquivo createUiDefinition.json](create-uidefinition-overview.md) para o seu aplicativo gerenciado, você precisa testar a experiência do usuário. Para simplificar o teste, use um ambiente de caixa de areia que carrega seu arquivo no portal. Não é necessário realmente implantar o aplicativo gerenciado. A caixa de areia apresenta sua interface de usuário na experiência atual do portal em tela cheia. Ou, você pode usar um script para testar a interface. As duas abordagens são mostradas neste artigo. A caixa de areia é a maneira recomendada de visualizar a interface.

## <a name="prerequisites"></a>Pré-requisitos

* Um arquivo **createUiDefinition.json**. Se você não tiver este arquivo, copie o [arquivo de amostra](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="use-sandbox"></a>Use caixa de areia

1. Abra a [caixa de areia de definição de ida de interface .](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![Mostrar caixa de areia](./media/test-createuidefinition/show-sandbox.png)

1. Substitua a definição vazia pelo conteúdo do seu arquivo createUiDefinition.json. Selecione **Visualização**.

   ![Selecione visualização](./media/test-createuidefinition/select-preview.png)

1. A forma que você criou é exibida. Você pode passar pela experiência do usuário e preencher os valores.

   ![Mostrar formulário](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Solução de problemas

Se o formulário não for exibido após a seleção **de Visualização,** você pode ter um erro de sintaxe. Procure o indicador vermelho na barra de rolagem direita e navegue até ele.

![Mostrar erro de sintaxe](./media/test-createuidefinition/show-syntax-error.png)

Se o seu formulário não for exibido e, em vez disso, você ver um ícone de uma nuvem com gota de lágrima, seu formulário tem um erro, como uma propriedade ausente. Abra as Ferramentas de Desenvolvedor Web em seu navegador. O **Console** exibe mensagens importantes sobre a interface.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Use o script de teste

Para testar a interface no portal, copie um dos scripts a seguir para o computador local:

* [Script de carga lateral PowerShell - Módulo AZ](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script de carga lateral PowerShell - Módulo Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de sideload do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Para ver o arquivo de interface no portal, execute o script baixado. O script cria uma conta de armazenamento na assinatura do Azure e carrega o arquivo createUiDefinition.json para a conta de armazenamento. A conta de armazenamento é criada na primeira vez que você executa o script ou em caso de exclusão da conta de armazenamento. Se a conta de armazenamento já existe em sua assinatura do Azure, o script a reutiliza. O script abre o portal e carrega o arquivo da conta de armazenamento.

Forneça um local para a conta de armazenamento e especifique a pasta que tem o arquivo createUiDefinition.json.

Para o PowerShell, use:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, use:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o arquivo createUiDefinition.json estiver na mesma pasta que o script e você já tiver criado a conta de armazenamento, você não precisará fornecer esses parâmetros.

Para o PowerShell, use:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Para a CLI do Azure, use:

```bash
./sideload-createuidef.sh
```

O script abre uma nova guia no navegador. Em seguida, exibe o portal com a interface para criar o aplicativo gerenciado.

Forneça valores para os campos. Quando concluído, você vê os valores que são passados para o modelo que podem ser encontrados no console de ferramentas de desenvolvedor do seu navegador.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Você pode usar esses valores como o arquivo de parâmetro para testar seu modelo de implantação.

Se o portal ficar pendurado na tela de resumo, pode haver um bug na seção de saída. Por exemplo, você pode ter referenciado um controle que não existe. Se um parâmetro na saída estiver vazio, o parâmetro pode estar fazendo referência a uma propriedade que não existe. Por exemplo, a referência para o controle é válida, mas a referência da propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar os arquivos de solução

Agora que você verificou que a interface do portal está funcionando conforme o esperado, é hora de validar que o arquivo createUiDefinition está corretamente integrado ao arquivo mainTemplate.json. É possível executar um teste de script de validação para testar o conteúdo dos arquivos de solução, incluindo o arquivo createUiDefinition. O script valida a sintaxe JSON, verifica expressões regex em campos de texto e garante que os valores de saída da interface do portal correspondam aos parâmetros do modelo. Para obter mais informações sobre como executar esse script, consulte [Executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Próximas etapas

Após validar a interface do portal, saiba como tornar o[aplicativo gerenciado do Azure disponível no Marketplace](publish-marketplace-app.md).
