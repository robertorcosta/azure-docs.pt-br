---
title: Criar um workspace
titleSuffix: ML Studio (classic) - Azure
description: Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esse workspace contém as ferramentas necessárias para criar, gerenciar e publicar testes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204453"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Criar e compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esse workspace contém as ferramentas necessárias para criar, gerenciar e publicar testes.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho do estúdio (clássico)

1. Entre no [Portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para entrar e criar um espaço de trabalho do estúdio (clássico), você precisa ser um administrador de assinatura do Azure. 
    >
    > 

2. Clique em **+Novo**

3. Na caixa de pesquisa, digite **Machine Learning Studio espaço de trabalho (clássico)** e selecione o item correspondente. Em seguida, selecione **Criar** na parte inferior da página.

4. Insira suas informações de workspace:

   - O *nome do workspace* pode ter até 260 caracteres, sem terminar com um espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
   - O *plano do serviço Web* escolhido (ou criado), juntamente com o *tipo de preço* associado, será usado se você implantar os serviços Web deste workspace.

     ![Criar um novo espaço de trabalho Studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

> [!NOTE]
> Machine Learning Studio (clássico) depende de uma conta de armazenamento do Azure que você fornece para salvar dados intermediários ao executar o fluxo de trabalho. Após o workspace ser criado, se a conta de armazenamento for excluída, ou se as chaves de acesso forem alteradas, o workspace deixará de funcionar e todos os testes nele falharão.
Se você excluir acidentalmente a conta de armazenamento, recrie-a com o mesmo nome e na mesma região da conta excluída e sincronize novamente a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, sincronize novamente as chaves de acesso na configuração do workspace usando o portal do Azure.

Depois que o espaço de trabalho for implantado, você poderá abri-lo no Machine Learning Studio (clássico).

1. Navegue até Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione seu workspace no canto superior direito.

    ![Selecione o workspace](./media/create-workspace/open-workspace.png)

3. Clique em **meus experimentos**.

    ![Experimentos abertos](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerenciar seu espaço de trabalho do estúdio (clássico), consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).
Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Machine Learning Studio (clássico)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)
Quando um espaço de trabalho Machine Learning Studio (clássico) é criado, você pode convidar usuários para seu espaço de trabalho para compartilhar o acesso ao seu espaço de trabalho e a todos os seus experimentos, conjuntos de anotações, notebooks, etc. Você pode adicionar usuários em uma das duas funções:

* **Usuário**: um usuário do workspace pode criar, abrir, modificar e excluir conjuntos de dados, experimentos etc. no workspace.
* **Proprietário**: um proprietário pode convidar e remover usuários no workspace, além do que o usuário pode fazer.

> [!NOTE]
> A conta de administrador que cria o workspace é adicionado automaticamente ao workspace como proprietário. No entanto, outros administradores ou usuários nessa assinatura não recebem automaticamente acesso ao workspace - é preciso convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para compartilhar um espaço de trabalho do Studio (clássico)

1. Entre no Machine Learning Studio (clássico) em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **CONFIGURAÇÕES**

3. Clique na guia **USUÁRIOS**

4. Clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de email. O usuário precisa de uma conta da Microsoft válida ou de uma conta organizacional (do Azure Active Directory).

6. Selecione se deseja adicionar os usuários como Proprietário ou Usuário.

7. Clique no botão de seleção **OK**.

Cada usuário que você adicionar receberá um email com instruções sobre como entrar no workspace compartilhado.

> [!NOTE]
> Para que os usuários possam implantar ou gerenciar os serviços Web neste workspace, eles devem ser colaboradores ou administradores na assinatura do Azure. 



