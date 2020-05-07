---
title: Treinar novamente modelos usando o designer de Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como treinar novamente modelos com pipelines publicados no designer de Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 77c1cc6ef5a7353d246958d1b047f054d5f49890
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837151"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Readaptar modelos com o designer do Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de instruções, você aprenderá a usar o Azure Machine Learning designer para treinar novamente um modelo de aprendizado de máquina. Você usará pipelines publicados para automatizar seu fluxo de trabalho e definir parâmetros para treinar seu modelo em novos dados. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de pipeline.
> * Publique seu pipeline de treinamento.
> * Retreine seu modelo com novos parâmetros.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace do Azure Machine Learning com o SKU Enterprise.
* Conclua a parte 1 desta série de instruções, [Transforme dados no designer](how-to-designer-transform-data.md).

Este artigo também pressupõe que você tenha conhecimento básico sobre a criação de pipelines no designer. Para obter uma introdução guiada, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline usado neste artigo é uma versão alterada do [exemplo 3: Previsão de renda](samples-designer.md#classification-samples). O pipeline usa o módulo [importar dados](algorithm-module-reference/import-data.md) , em vez de usar o conjunto de exemplo para mostrar como treinar modelos usando seus próprios dados.

![Captura de tela que mostra o pipeline de exemplo modificado com uma caixa realçando o módulo importar dados](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de pipeline

Crie parâmetros de pipeline para definir variáveis dinamicamente em tempo de execução. Para este exemplo, você vai alterar o caminho de dados de treinamento de um valor fixo para um parâmetro, para que você possa treinar novamente seu modelo em dados diferentes.

1. Selecione o módulo **importar dados** .

    > [!NOTE]
    > Este exemplo usa o módulo importar dados para acessar dados em um repositório de armazenamento registrado. No entanto, você pode seguir etapas semelhantes se usar padrões de acesso a dados alternativos.

1. No painel de detalhes do módulo, à direita da tela, selecione sua fonte de dados.

1. Insira o caminho para os dados. Você também pode selecionar **procurar caminho** para procurar sua árvore de arquivos. 

1. Permouseover o campo **caminho** e selecione as reticências acima do campo **caminho** exibido.

    ![Captura de tela que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecione **Adicionar ao parâmetro de pipeline**.

1. Forneça um nome de parâmetro e um valor padrão.

   > [!NOTE]
   > Você pode inspecionar e editar os parâmetros de pipeline selecionando o ícone de engrenagem **configurações** ao lado do título do rascunho do seu pipeline. 

1. Clique em **Salvar**.

1. Envie a execução do pipeline.

## <a name="find-a-trained-model"></a>Encontrar um modelo treinado

O designer salva toda a saída de pipeline, incluindo modelos treinados, para a conta de armazenamento de espaço de trabalho padrão. Você também pode acessar modelos treinados diretamente no designer:

1. Aguarde até que o pipeline termine a execução.
1. Selecione o módulo **Treinar Modelo**.
1. No painel detalhes do módulo, à direita da tela, selecione **saídas + logs**.
1. Você pode encontrar seu modelo em **outras saídas** junto com os logs de execução.
1. Como alternativa, selecione o ícone **Exibir saída** . A partir daqui, você pode seguir a instrução na caixa de diálogo para navegar diretamente para seu repositório de armazenamento. 

![Captura de tela que mostra como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de treinamento

Publique um pipeline em um ponto de extremidade de pipeline para reutilizar facilmente seus pipelines no futuro. Um ponto de extremidade de pipeline cria um ponto de extremidade REST para invocar o pipeline no futuro. Neste exemplo, o ponto de extremidade do pipeline permite reutilizar seu pipeline para treinar novamente um modelo em dados diferentes.

1. Selecione **publicar** acima da tela do designer.
1. Selecione ou crie um ponto de extremidade de pipeline.

   > [!NOTE]
   > Você pode publicar vários pipelines em um único ponto de extremidade. Cada pipeline em um determinado ponto de extremidade recebe um número de versão, que você pode especificar ao chamar o ponto de extremidade do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Treinar novamente seu modelo

Agora que você tem um pipeline de treinamento publicado, você pode usá-lo para treinar novamente seu modelo em novos dados. Você pode enviar execuções de um ponto de extremidade de pipeline do espaço de trabalho do estúdio ou programaticamente.

### <a name="submit-runs-by-using-the-designer"></a>Enviar execuções usando o designer

Use as etapas a seguir para enviar um ponto de extremidade de pipeline com parâmetros executado do designer:

1. Vá para a página **pontos de extremidade** no seu espaço de trabalho do estúdio.
1. Selecione a guia **pontos de extremidade do pipeline** . Em seguida, selecione o ponto de extremidade do pipeline.
1. Selecione a guia **pipelines publicados** . Em seguida, selecione a versão de pipeline que você deseja executar.
1. Selecione **Enviar**.
1. Na caixa de diálogo instalação, você pode especificar os valores dos parâmetros para a execução. Para este exemplo, atualize o caminho de dados para treinar seu modelo usando um conjunto que não seja dos EUA.

![Captura de tela que mostra como configurar uma execução de pipeline com parâmetros no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Enviar execuções usando código

Você pode encontrar o ponto de extremidade REST de um pipeline publicado no painel Visão geral. Ao chamar o ponto de extremidade, você pode treinar novamente o pipeline publicado.

Para fazer uma chamada REST, você precisa de um cabeçalho de autenticação do tipo portador OAuth 2,0. Para obter informações sobre como configurar a autenticação para seu espaço de trabalho e fazer uma chamada REST com parâmetros, consulte [criar um pipeline de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar um ponto de extremidade de pipeline de treinamento com parâmetros usando o designer.

Para obter uma explicação completa de como você pode implantar um modelo para fazer previsões, consulte o [tutorial do designer](tutorial-designer-automobile-price-train-score.md) para treinar e implantar um modelo de regressão.
