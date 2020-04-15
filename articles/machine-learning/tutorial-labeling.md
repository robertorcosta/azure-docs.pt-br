---
title: 'Tutorial: Criar um projeto de rotulagem para classificação de imagens'
titleSuffix: Azure Machine Learning
description: Saiba como gerenciar o processo de rotulagem de imagens para que elas possam ser usadas em modelos de classificação de imagem de várias classes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879618"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Tutorial: Criar um projeto de rotulagem para classificação de imagens de várias classes 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial mostra como gerenciar o processo de rotulagem (também conhecido como marcação) de imagens a serem usadas como dados para a criação de modelos de machine learning. A rotulagem de dados no Azure Machine Learning está em versão prévia pública.

Se você quiser treinar um modelo de machine learning para classificar imagens, precisará de centenas ou até milhares de imagens rotuladas corretamente.  O Azure Machine Learning ajuda a gerenciar o progresso de sua equipe privada de especialistas em domínio à medida que rotulam os dados.
 
Neste tutorial, você usará imagens de gatos e cachorros.  Como cada imagem é um gato ou um cachorro, este é um projeto de rotulagem de *várias classes*. Você aprenderá a:

> [!div class="checklist"]
>
> * Criar uma conta de armazenamento do Azure e carregar imagens na conta.
> * Criar um Workspace de Azure Machine Learning.
> * Crie um projeto de rotulagem de imagem de várias classes.
> * Rotular seus dados.  Você ou seus rotuladores podem executar essa tarefa.
> * Concluir o projeto revisando e exportando os dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço.

Você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Iniciar um projeto de rotulagem

Agora, você gerenciará o projeto de rotulagem no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de todos os níveis de habilidade. O estúdio não é compatível com navegadores Internet Explorer.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Selecione a assinatura e o workspace criado.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Criar um armazenamento de dados

Os armazenamentos de dados do Azure Machine Learning são usados para armazenar informações de conexão, como sua ID de assinatura e autorização de token. Aqui você usa um armazenamento de dados para se conectar à conta de armazenamento que contém as imagens deste tutorial.

1. No lado esquerdo do workspace, selecione **Armazenamentos de dados**.

1. Selecione **+ Novo armazenamento de dados**.

1. Preencha o formulário com estas configurações:

    Campo|Descrição 
    ---|---
    Nome do armazenamento de dados | Dê um nome ao armazenamento de dados.  Aqui, usamos **labeling_tutorial**.
    Tipo de armazenamento de dados | Selecionar o tipo de armazenamento.  Aqui, usamos **Armazenamento de Blobs do Azure**, o armazenamento preferencial para imagens.
    Método de seleção de conta | Selecione **Inserir manualmente**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Tipo de autenticação | Selecione **Token SAS**.
    Chave de conta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecione **Criar** para criar o armazenamento de dados.

### <a name="add-labelers-to-workspace"></a>Adicionar rotuladores ao workspace

Configure seu workspace para incluir todas as pessoas que rotularão os dados de qualquer um dos seus projetos.  Posteriormente, você adicionará esses rotuladores ao projeto de rotulagem específico.

1. No lado esquerdo, selecione **Rotulagem de dados**.

1. Na parte superior da página, selecione **Rotuladores**.

1. Selecione **Adicionar rotulador** para adicionar o endereço de email de um rotulador.

1. Continue a adicionar mais rotuladores até terminar.

### <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Agora que você tem a lista de rotuladores e o acesso aos dados que deseja rotular, crie o projeto de rotulagem.

1. Na parte superior da página, selecione **Projetos**.

1. Selecione **+ Adicionar projeto**.

    ![Criar um projeto](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Detalhes do projeto

1. Use a seguinte entrada no formulário **Detalhes do projeto**:

    Campo|Descrição 
    ---|---
    Nome do projeto | Dê um nome ao seu projeto.  Aqui, usaremos **tutorial-cats-n-dogs**.
    Rotulando o tipo de tarefa | Selecione **Classificação de Imagem de Várias Classes**.  
    
    Selecione **Avançar** para continuar a criar o projeto.

### <a name="select-or-create-a-dataset"></a>Selecionar ou criar um conjunto de dados

1.   No formulário **Selecionar ou criar um conjunto de dados**, selecione a segunda opção, **Criar um conjunto de dados** e, em seguida, selecione o link **Do armazenamento de dados**.

1. Use a seguinte entrada para o formulário **Criar conjunto de dados por meio do armazenamento de dados**:

    1. No formulário **Informações básicas**, adicione um nome; aqui usaremos **images-for-tutorial**.  Adicione uma descrição se desejar.  Em seguida, selecione **Avançar**.
    1. No formulário **Seleção de armazenamento de dados**, use a lista suspensa para selecionar o **Armazenamento de dados criado anteriormente**, por exemplo **tutorial_images (Armazenamento de Blobs do Azure)**
    1. Em seguida, ainda no formulário **Seleção de armazenamento de dados**, selecione **Procurar** e, em seguida, selecione **MultiClass – DogsCats**.  Selecione **Salvar** para usar **/MultiClass – DogsCats** como o caminho.
    1. Selecione **Avançar** para confirmar os detalhes e, em seguida, **Criar** para criar o conjunto de dados.
    1. Selecione o círculo ao lado do nome do conjunto de dados na lista, por exemplo **images-for-tutorial**.

1. Selecione **Avançar** para continuar a criar o projeto.

### <a name="label-classes"></a>Classes de rótulos

1. No formulário **Classes de rótulos**, digite um nome de rótulo e, em seguida, selecione **+ Adicionar rótulo** para digitar o próximo rótulo.  Para este projeto, os rótulos são **Gato**, **Cachorro** e **Incerto**.

1. Selecione **Avançar** quando tiver adicionado todos os rótulos.

### <a name="labeling-instructions"></a>Instruções de rotulagem

1. No formulário **Instruções de rotulagem**, você pode fornecer um link para um site que forneça instruções detalhadas para seus rotuladores.  Deixaremos em branco para este tutorial.

1. Você também pode adicionar uma breve descrição da tarefa diretamente no formulário.  Digite **Tutorial de rotulagem – gatos e cachorros.**

1. Selecione **Avançar**.

1. No formulário **Rotulagem assistida por ML**, deixe a caixa de seleção desmarcada. A rotulagem assistida por ML requer mais dados do que você usará neste tutorial.

1. Selecione **Criar projeto**.

Essa página não é atualizada automaticamente. Após uma pausa, atualize manualmente a página até o status do projeto mudar para **Criado**.

### <a name="add-labelers-to-your-project"></a>Adicionar rotuladores ao seu projeto

Adicione alguns ou todos os seus rotuladores a este projeto.

1. Selecione o nome do projeto para abri-lo.  

1. Na parte superior da página, selecione **Equipes**.

1. Selecione o link **Equipe Padrão de labeling_tutorial**.

1. Agora, use **Atribuir rotuladores** para adicionar os rotuladores que você deseja que participem deste projeto. 

1. Selecione na lista de rotuladores que você criou anteriormente.  Depois de selecionar todos os rotuladores que você deseja usar, selecione **Atribuir rotuladores** para adicioná-los à sua equipe de projeto padrão.

## <a name="start-labeling"></a>Iniciar rotulagem

Você já configurou os recursos do Azure e configurou um projeto de rotulagem de dados. Agora é hora de adicionar rótulos aos seus dados.

### <a name="notify-labelers"></a>Notificar rotuladores

Se você tiver muitas imagens a serem rotuladas, espera-se que também tenha muitos rotuladores para realizar a tarefa.  Agora, você precisa enviar instruções para que eles possam acessar os dados e começar a rotular.

1. Em [Machine Learning Studio](https://ml.azure.com), selecione **Rotulagem de dados** no lado esquerdo para localizar seu projeto.  

1. Selecione o link do nome do projeto.

1. Na parte superior da página, selecione **Detalhes**.  Você verá um resumo do seu projeto.

    ![Detalhes do projeto](media/tutorial-labeling/project-details.png)

1. Copie o link da **URL do portal de rotulagem** para enviar aos seus rotuladores.

1. Agora, selecione **Equipe** na parte superior para localizar sua equipe de rotulagem.  

1. Selecione o link do nome da equipe.

1. Na parte superior da página, selecione **Enviar email para a equipe** para iniciar seu email.  Cole a URL do portal de rotulagem que você acabou de copiar.  

Sempre que um rotulador acessar a URL do portal, ele receberá mais imagens para rotular, até que a fila esteja vazia.  

### <a name="tag-the-images"></a>Marcar as imagens

Nesta parte do tutorial, você mudará da função de *administrador de projeto* para a de um *rotulador*.  Use a URL que você enviou para a equipe.  Essa URL o leva para o portal de rotulagem do seu projeto.  Se você tivesse adicionado instruções, você as veria aqui quando chegasse na página.

1. Na parte superior da página, selecione **Tarefas** para iniciar a rotulagem.

1. Selecione uma imagem em miniatura à direita para exibir o número de imagens que você deseja rotular de uma vez. Você deve rotular todas essas imagens antes de continuar. Só alterne entre os layouts quando tiver uma página nova de dados sem rótulo. Alternar entre layouts limpa o trabalho de marcação em andamento na página.

1. Selecione uma ou mais imagens e, em seguida, selecione uma tag para aplicar à seleção. A tag aparece abaixo da imagem.  Continue a selecionar e marcar todas as imagens na página.  Para selecionar todas as imagens exibidas simultaneamente, selecione **Selecionar tudo**. Selecione pelo menos uma imagem para aplicar uma tag.


    > [!TIP]
    > Selecione as nove primeiras marcas usando as teclas numéricas do teclado.

1. Depois que todas as imagens na página forem marcadas, selecione **Enviar** para enviar esses rótulos.

    ![Marcar imagens](media/tutorial-labeling/catsndogs.gif)

1. Depois que você enviar marcas para os dados à disposição, o Azure atualizará a página com um novo conjunto de imagens da fila de trabalho.

## <a name="complete-the-project"></a>Concluir o projeto

Agora você voltará para a função de *administrador de projeto* para o projeto de rotulagem.

Como gerente, pode ser interessante examinar o trabalho do seu rotulador.  

### <a name="review-labeled-data"></a>Examinar dados rotulados

1. Em [Machine Learning Studio](https://ml.azure.com), selecione **Rotulagem de dados** no lado esquerdo para localizar seu projeto.  

1. Selecione o link do nome do projeto.

1. O painel mostra o progresso do seu projeto.

1. Na parte superior da página, selecione **Dados**.

1. No lado esquerdo, selecione **Dados rotulados** para ver as imagens marcadas.  

1. Se você discordar de um rótulo, selecione a imagem e, em seguida, selecione **Rejeitar** na parte inferior da página.  As tags serão removidas e a imagem será colocada de volta na fila de imagens sem rótulo.

### <a name="export-labeled-data"></a>Exportar dados rotulados

Você pode exportar os dados de rótulo para experimentação no Machine Learning a qualquer momento. Os usuários geralmente exportam várias vezes e treinam modelos diferentes, em vez de esperar que todas as imagens sejam rotuladas.

Os rótulos de imagens podem ser exportados no [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados do Azure Machine Learning. O formato do conjunto de dados é fácil de usar para treinamento no Azure Machine Learning.  

1. Em [Machine Learning Studio](https://ml.azure.com), selecione **Rotulagem de dados** no lado esquerdo para localizar seu projeto.  

1. Selecione o link do nome do projeto.

1. Selecione **Exportar** e escolha **Exportar como conjunto de dados do Azure ML**. 

    O status da exportação aparece logo abaixo do botão **Exportar**. 

1. Depois que os rótulos forem exportados com êxito, selecione **Conjuntos de dados** no lado esquerdo para ver os resultados.

## <a name="clean-up-resources"></a>Limpar os recursos


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você rotulou imagens.  Agora, use os dados rotulados:

> [!div class="nextstepaction"]
> [Treinar um modelo de machine learning de reconhecimento de imagens](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
