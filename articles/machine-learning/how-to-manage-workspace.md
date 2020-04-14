---
title: Crie espaços de trabalho de Machine Learning do Azure no portal
titleSuffix: Azure Machine Learning
description: Aprenda a criar, visualizar e excluir espaços de trabalho do Azure Machine Learning no portal Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4b4b64bcca57e1dc98cdba10626597532ae1461c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269714"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Crie e gerencie espaços de trabalho de Machine Learning do Azure no portal Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você criará, visualizará e excluirá [**espaços de trabalho do Azure Machine Learning**](concept-workspace.md) no portal Azure para Aprendizado de Máquina do [Azure](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas à medida que suas necessidades mudam ou os requisitos para automação aumentam, você também pode criar e excluir espaços de trabalho [usando o CLI,](reference-azure-machine-learning-cli.md) [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou através da [extensão VS Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Criar um workspace

Para criar um workspace, você precisa de uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

1. Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure. 

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning**, selecione **Criar** para começar.

1. Forneça as informações a seguir para configurar o novo workspace:

   Campo|Descrição 
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de workspaces criados por outras pessoas. O nome do espaço de trabalho é insensível ao caso.
   Subscription |Selecione a assinatura do Azure que você deseja usar.
   Resource group | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um grupo de recursos. Um grupo de recursos mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. 
   Location | Selecione a localização mais próxima aos usuários e recursos de dados para criar o workspace.
   Edição do Workspace | Selecione **Básico** ou **Enterprise**.  Esta edição do espaço de trabalho determina os recursos aos quais você terá acesso e preços. Saiba mais sobre [as ofertas de edições Básicas e Corporativas.](overview-what-is-azure-ml.md#sku) 

    ![Configurar seu novo workspace](./media/how-to-manage-workspace/select-edition.png)

1. Quando terminar de configurar o espaço de trabalho, selecione **'Revisar + Criar ''Revisar'.**
2. Revise as configurações e faça quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as configurações, selecione **Criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar seu workspace na nuvem.

   Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. 
 
 1. Para exibir o novo workspace, selecione **Ir para o recurso**.

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você estiver criando uma [instância de computação,](tutorial-1st-experiment-sdk-setup.md#azure)pule este passo.

1. Se você planeja usar código em seu ambiente local que referencia esse workspace, selecione **Baixar config.json** da seção **Visão geral** do workspace.  

   ![Baixe o config.json](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts de Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório denominado *.azureml* ou em um diretório pai. Quando você cria uma instância de computação, este arquivo é adicionado ao diretório correto na VM para você.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade para edição Enterprise

Você pode atualizar seu espaço de trabalho da edição Básica para a edição Enterprise para aproveitar os recursos aprimorados, como experiências de baixo código e recursos de segurança aprimorados.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Selecione o espaço de trabalho que deseja atualizar.

1. Selecione **Saiba mais** no canto superior direito da página.

   [![Atualize um](./media/how-to-manage-workspace/upgrade.png) espaço de trabalho](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecione **Atualizar** na janela que aparece.


> [!IMPORTANT]
> Não é possível fazer o downgrade de um espaço de trabalho de edição Enterprise para um espaço de trabalho de edição básica. 

## <a name="find-a-workspace"></a><a name="view"></a>Encontre um espaço de trabalho

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procure espaço de trabalho para aprendizado de máquina do Azure](./media/how-to-manage-workspace/find-workspaces.png)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

## <a name="delete-a-workspace"></a>Excluir um workspace

No [portal Azure,](https://portal.azure.com/) **selecione Excluir** na parte superior do espaço de trabalho que deseja excluir.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Excluir workspace":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

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

Siga o tutorial completo para aprender como usar um espaço de trabalho para construir, treinar e implantar modelos com o Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
