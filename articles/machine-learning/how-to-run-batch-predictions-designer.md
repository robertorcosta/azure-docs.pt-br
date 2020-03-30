---
title: Executar previsões em lote usando o designer do Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como treinar um modelo e configurar um pipeline de previsão em lote usando o designer. Implante o pipeline como um serviço Web com parâmetro, que pode ser disparado de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477214"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Executar previsões em lote usando o designer do Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprende a usar o designer para criar um pipeline de previsão de lote. A previsão de lote permite que você marque continuamente grandes conjuntos de dados sob demanda usando um serviço web que pode ser acionado a partir de qualquer biblioteca HTTP.

Neste como fazer, você aprende a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e publicar um pipeline de inferência em lote
> * Consumir um ponto final do gasoduto
> * Gerenciar versões de ponto final

Para saber como configurar serviços de pontuação em lote usando o SDK, consulte o [como acompanhar](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Pré-requisitos

Isso como assumir que você já tem um oleoduto de treinamento. Para uma introdução guiada ao designer, complete [a primeira parte do tutorial do designer.](tutorial-designer-automobile-price-train-score.md) 

## <a name="create-a-batch-inference-pipeline"></a>Criar um pipeline de inferência em lote

Seu pipeline de treinamento deve ser executado pelo menos uma vez para ser capaz de criar um oleoduto de inferência.

1. Vá para a guia **Designer** em seu espaço de trabalho.

1. Selecione o pipeline de treinamento que treina o modelo que você deseja usar para fazer a previsão.

1. **Envie** o oleoduto.

    ![Enviar o pipeline](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Agora que o oleoduto de treinamento foi executado, você pode criar um pipeline de inferência em lote.

1. Ao lado **de Enviar,** selecione o novo **pipeline de inferência de inferência de demissão**.

1. Selecione **o pipeline de inferência em lote**.

    ![Criar pipeline de inferência em lote](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
O resultado é um pipeline de inferência em lote padrão. 

### <a name="add-a-pipeline-parameter"></a>Adicione um parâmetro de tubulação

Para criar previsões sobre novos dados, você pode conectar manualmente um conjunto de dados diferente nesta exibição de rascunho de pipeline ou criar um parâmetro para o seu conjunto de dados. Os parâmetros permitem alterar o comportamento do processo de inferência em lote em tempo de execução.

Nesta seção, você cria um parâmetro de conjunto de dados para especificar um conjunto de dados diferente para fazer previsões.

1. Selecione o módulo de conjunto de dados.

1. Um painel aparecerá à direita da tela. Na parte inferior do painel, selecione **Definir como parâmetro de tubulação**.
   
    Digite um nome para o parâmetro ou aceite o valor padrão.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publique seu pipeline de inferência de lotes

Agora você está pronto para implantar o oleoduto de inferência. Isso irá implantar o gasoduto e torná-lo disponível para outros usarem.

1. Clique no botão **Publicar**.

1. Na caixa de diálogo que aparece, expanda a queda para **PipelineEndpoint**e selecione **Novo PipelineEndpoint**.

1. Forneça um nome de ponto final e uma descrição opcional.

    Perto da parte inferior da caixa de diálogo, você pode ver o parâmetro configurado com um valor padrão do ID do conjunto de dados usado durante o treinamento.

1. Selecione **Publicar**.

![Publicar um pipeline](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Consumir um ponto final

Agora, você tem um pipeline publicado com um parâmetro de conjunto de dados. O pipeline usará o modelo treinado criado no pipeline de treinamento para marcar o conjunto de dados que você fornece como parâmetro.

### <a name="submit-a-pipeline-run"></a>Enviar uma execução de gasodutos 

Nesta seção, você configurará uma execução manual do pipeline e alterará o parâmetro do pipeline para marcar novos dados. 

1. Depois que a implantação estiver concluída, vá para a seção **Endpoints.**

1. Selecione **pontos finais do Pipeline**.

1. Selecione o nome do ponto final que você criou.

![Link do ponto de extremidade](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selecione **pipelines publicados**.

    Esta tela mostra todos os pipelines publicados publicados este ponto final.

1. Selecione o pipeline que você publicou.

    A página de detalhes do pipeline mostra um histórico de execução detalhado e informações de seqüência de conexão para o seu pipeline. 
    
1. Selecione **Enviar** para criar uma execução manual do pipeline.

    ![Detalhes do pipeline](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Alterar o parâmetro para usar um conjunto de dados diferente.
    
1. Selecione **Enviar** para executar o pipeline.

### <a name="use-the-rest-endpoint"></a>Use o ponto final REST

Você pode encontrar informações sobre como consumir pontos finais de pipeline e o pipeline publicado na seção **Endpoints.**

Você pode encontrar o ponto final REST de um ponto final do pipeline no painel de visão geral de execução. Ao chamar o ponto final, você está consumindo seu pipeline publicado padrão.

Você também pode consumir um pipeline publicado na página **de pipelines publicados.** Selecione um pipeline publicado e encontre o ponto final rest dele. 

![Detalhes do ponto final de descanso](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação tipo OAuth 2.0. Confira a [seção do tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) a seguir para obter mais detalhes sobre como configurar a autenticação para seu workspace e realizar uma chamada REST.

## <a name="versioning-endpoints"></a>Versionando pontos finais

O designer atribui uma versão a cada pipeline subseqüente que você publica para um ponto final. Você pode especificar a versão de pipeline que deseja executar como parâmetro em sua chamada REST. Se você não especificar um número de versão, o designer usará o pipeline padrão.

Quando você publica um pipeline, você pode optar por torná-lo o novo pipeline padrão para esse ponto final.

![Definir pipeline padrão](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Você também pode definir um novo pipeline padrão na guia **'Pipelines Published'** do seu ponto final.

![Definir pipeline padrão](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) de designer para treinar e implantar um modelo de regressão.
''