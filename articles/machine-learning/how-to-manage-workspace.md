---
title: Criar Azure Machine Learning espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 18176d8e99a07e05812b5cb789dc258e675b4f9e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75537078"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerenciar espaços de trabalho de Azure Machine Learning no portal do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você criará, exibirá e excluirá [**espaços de trabalho Azure Machine Learning**](concept-workspace.md) no portal do Azure para [Azure Machine Learning](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas conforme suas necessidades mudam ou requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md), [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [por meio da extensão vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Crie um workspace

Para criar um workspace, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

1. Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure. 

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning**, selecione **Criar** para começar.

1. Forneça as informações a seguir para configurar o novo workspace:

   Campo|Description 
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de workspaces criados por outras pessoas.  
   Subscription |Selecione a assinatura do Azure que você deseja usar.
   Grupo de recursos | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um grupo de recursos. Um grupo de recursos mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. 
   Local | Selecione a localização mais próxima aos usuários e recursos de dados para criar o workspace.
   Edição do Workspace | Selecione **básico** ou **Enterprise**.  Essa edição de espaço de trabalho determina os recursos aos quais você terá acesso e preços. Saiba mais sobre as [ofertas Basic e Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Configurar seu novo workspace](./media/how-to-manage-workspace/select-edition.png)

1. Quando terminar de configurar o workspace, selecione **Criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu workspace na nuvem.

   Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. 
 
 1. Para exibir o novo workspace, selecione **Ir para o recurso**.

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você for criar uma [instância de computação](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.

1. Se você planeja usar código em seu ambiente local que referencia esse workspace, selecione **Baixar config.json** da seção **Visão geral** do workspace.  

   ![Baixe o config.json](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts de Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório denominado *.azureml* ou em um diretório pai. Quando você cria uma instância de computação, esse arquivo é adicionado ao diretório correto na VM para você.

## <a name="upgrade"></a>Atualizar para o Enterprise Edition

Você pode atualizar seu espaço de trabalho de edição básica para Enterprise Edition para aproveitar os recursos aprimorados, como experiências de código baixo e recursos de segurança aprimorados.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Selecione o espaço de trabalho que você deseja atualizar.

1. Selecione **saiba mais** na parte superior direita da página.

   [![atualizar um espaço de trabalho](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecione **Atualizar** na janela exibida.


> [!IMPORTANT]
> Não é possível fazer downgrade de um espaço de trabalho Enterprise Edition para um espaço de trabalho básico. 

## <a name="view"></a>Localizar um espaço de trabalho

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procurar Azure Machine Learning espaço de trabalho](./media/how-to-manage-workspace/find-workspaces.png)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

## <a name="delete-a-workspace"></a>Excluir um workspace

Use o botão Excluir na parte superior do workspace que você deseja excluir.

  ![Botão Excluir](./media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximos passos

Siga o tutorial de tamanho completo para aprender a usar um espaço de trabalho para criar, treinar e implantar modelos com o Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
