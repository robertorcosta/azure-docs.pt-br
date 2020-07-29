---
title: Readaptação de modelos usando o designer do Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como readaptar modelos com pipelines publicados no designer do Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.custom: designer
ms.openlocfilehash: c466684bf5c07b5e88e8052c29aa9fb0b8583a89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430063"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Readaptação de modelos com o designer do Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de instruções, você aprenderá a usar o designer do Azure Machine Learning para readaptar um modelo de machine learning. Você usará pipelines publicados para automatizar seu fluxo de trabalho e definir parâmetros para treinar seu modelo para novos dados. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de pipeline.
> * Publique seu pipeline de treinamento.
> * Readapte seu modelo com novos parâmetros.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace do Azure Machine Learning com o SKU Enterprise.
* Conclua a parte 1 desta série de instruções, [Transformação de dados no designer](how-to-designer-transform-data.md).

Este artigo também pressupõe que você tenha conhecimentos básicos sobre a criação de pipelines no designer. Para obter uma introdução guiada, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline usado neste artigo é uma versão alterada do Exemplo 3 [: Previsão de receita](samples-designer.md#classification). O pipeline usa o módulo [Importar Dados](algorithm-module-reference/import-data.md), em vez de usar um conjunto de dados de exemplo para mostrar a você como treinar modelos usando seus próprios dados.

![Captura de tela que mostra o pipeline de exemplo modificado com uma caixa realçando o módulo Importar Dados](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Criação de um parâmetro de pipeline

Crie parâmetros de pipeline para definir variáveis dinamicamente em tempo de execução. Para este exemplo, você irá alterar o caminho de dados de treinamento de um valor fixo para um parâmetro, para que você possa readaptar seu modelo para dados diferentes.

1. Selecione o módulo **Importar Dados**.

    > [!NOTE]
    > Este exemplo usa o módulo Importar Dados para acessar dados em um armazenamento de dados registrado. No entanto, você pode seguir etapas semelhantes se usar padrões de acesso a dados alternativos.

1. No painel de detalhes do módulo à direita da tela, selecione a sua fonte de dados.

1. Digite o caminho para os seus dados. Você também pode selecionar **Procurar caminho** para procurar em sua árvore de arquivos. 

1. Passe com o cursor do mouse sobre o campo **Caminho** e selecione as reticências exibidas acima do campo **Caminho**.

    ![Captura de tela que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecione **Adicionar ao parâmetro de pipeline**.

1. Forneça um nome de parâmetro e um valor padrão.

   > [!NOTE]
   > Você pode inspecionar e editar seus parâmetros de pipeline selecionando o ícone de engrenagem de **Configurações** ao lado do título do seu rascunho de pipeline. 

1. Clique em **Salvar**.

1. Envie a execução do pipeline.

## <a name="find-a-trained-model"></a>Localização de um modelo treinado

O designer salva toda a saída do pipeline, incluindo modelos treinados, na conta de armazenamento de workspace padrão. Você também pode acessar modelos treinados diretamente no designer:

1. Aguarde até que o pipeline termine a execução.
1. Selecione o módulo **Treinar Modelo**.
1. No painel de detalhes do módulo à direita da tela, selecione **Saídas + logs**.
1. Encontre o modelo em **Outras saídas** juntamente com os logs de execução.
1. Como alternativa, selecione o ícone **Exibir saída**. A partir deste ponto, siga as instruções na caixa de diálogo para navegar diretamente até o seu armazenamento de dados. 

![Captura de tela que mostra como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicação de um pipeline de treinamento

Publique um pipeline em um ponto de extremidade de pipeline para reutilizar facilmente seus pipelines no futuro. Um ponto de extremidade de pipeline cria um ponto de extremidade REST para invocar o pipeline no futuro. Neste exemplo, o ponto de extremidade do pipeline permite reutilizar o pipeline para readaptar um modelo para dados diferentes.

1. Selecione **Publicar** acima da tela do designer.
1. Selecione ou crie um ponto de extremidade de pipeline.

   > [!NOTE]
   > Você pode publicar vários pipelines em um único ponto de extremidade. Cada pipeline em um determinado ponto de extremidade recebe um número de versão, que você pode especificar quando chama o ponto de extremidade do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Readaptação do modelo

Agora que você publicou um pipeline de treinamento, use-o para readaptar seu modelo para novos dados. Envie execuções de um ponto de extremidade de pipeline do workspace do estúdio ou programaticamente.

### <a name="submit-runs-by-using-the-designer"></a>Envio de execuções usando o designer

Siga as etapas a seguir para enviar uma execução de ponto de extremidade de pipeline com parâmetros a partir do designer:

1. Acesse a página **Pontos de extremidade** no workspace do estúdio.
1. Selecione a guia **Pontos de extremidade de pipeline**. Em seguida, selecione o ponto de extremidade do pipeline.
1. Selecione a guia **Pipelines publicados**. Em seguida, selecione a versão do pipeline que você deseja executar.
1. Selecione **Enviar**.
1. Na caixa de diálogo de configuração, especifique os valores dos parâmetros para a execução. Para este exemplo, atualize o caminho de dados para treinar seu modelo usando um conjunto de dados que não seja dos EUA.

![Captura de tela que mostra como configurar uma execução de pipeline com parâmetros no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Envio de execuções usando código

Encontre o ponto de extremidade de REST de um pipeline publicado no painel de visão geral. É possível readaptar o pipeline publicado chamando o ponto de extremidade.

Para realizar uma chamada REST, você precisa de um cabeçalho de autenticação do tipo portador do OAuth 2.0. Para obter informações sobre como configurar a autenticação do seu workspace e fazer uma chamada REST com parâmetros, veja [Criação de um pipeline do Azure Machine Learning para pontuação em lote](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar um ponto de extremidade de pipeline de treinamento com parâmetros usando o designer.

Para obter uma explicação completa de como você pode implantar um modelo para fazer previsões, veja o [tutorial do designer](tutorial-designer-automobile-price-train-score.md) para treinar e implantar um modelo de regressão.
