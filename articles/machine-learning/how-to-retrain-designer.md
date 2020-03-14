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
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368155"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Treinar novamente modelos com o designer de Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de instruções, você aprenderá a usar o Azure Machine Learning designer para treinar novamente um modelo de aprendizado de máquina. Descubra como usar pipelines publicados para automatizar fluxos de trabalho de aprendizado de máquina para readaptação.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de pipeline.
> * Publique seu pipeline de treinamento.
> * Readaptação de seu modelo.

## <a name="prerequisites"></a>Prerequisites

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).
* Um espaço de trabalho Azure Machine Learning com o SKU corporativo.

Este artigo pressupõe que você tenha conhecimento básico sobre a criação de pipelines no designer. Para obter uma introdução guiada ao designer, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline usado neste artigo é uma versão alterada do que foi encontrada no [exemplo 3: Previsão de renda](how-to-designer-sample-classification-predict-income.md). Ele usa o módulo [importar dados](algorithm-module-reference/import-data.md) em vez do conjunto de exemplo para mostrar como treinar um modelo usando seus próprios dados.

![Captura de tela que mostra o pipeline de exemplo modificado com uma caixa realçando o módulo importar dados](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Para treinar novamente um modelo, você precisa de um modelo inicial. Nesta seção, você aprenderá a treinar um modelo e acessar o modelo salvo usando o designer.

1. Selecione o módulo **importar dados** .
1. No painel Propriedades, especifique uma fonte de dados.

   ![Captura de tela que mostra uma configuração de exemplo do módulo importar dados](./media/how-to-retrain-designer/import-data-settings.png)

   Para este exemplo, os dados são armazenados em um [Azure datastore](how-to-access-data.md). Se você ainda não tiver um repositório de armazenamento, poderá criar um agora selecionando **novo repositório de armazenamento**.

1. Especifique o caminho para seus dados. Você também pode selecionar **procurar caminho** para navegar até seu repositório de armazenamento. 
1. Selecione **executar** na parte superior da tela.
    
   > [!NOTE]
   > Se você já tiver definido uma computação padrão para esse rascunho de pipeline, o pipeline será executado automaticamente. Caso contrário, você pode seguir os prompts no painel configurações para definir um agora.

### <a name="find-your-trained-model"></a>Encontre seu modelo treinado

O designer salva todas as saídas de pipeline, incluindo modelos treinados, para a conta de armazenamento padrão. Você também pode acessar o modelo treinado diretamente no designer:

1. Aguarde até que o pipeline termine a execução.
1. Selecione o módulo **Treinar Modelo**.
1. No painel configurações, selecione **saídas + logs**.
1. Selecione o ícone **Exibir saída** e siga as instruções na janela pop-up para localizar o modelo treinado.

![Captura de tela que mostra como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de pipeline

Adicione parâmetros de pipeline para definir variáveis dinamicamente em tempo de execução. Para este pipeline, adicione um parâmetro para o caminho de dados de treinamento para que você possa treinar novamente seu modelo em um novo conjunto.

1. Selecione o módulo **importar dados** .
1. No painel configurações, selecione as reticências acima do campo **caminho** .
1. Selecione **Adicionar ao parâmetro de pipeline**.
1. Forneça um nome de parâmetro e um valor padrão.

   > [!NOTE]
   > Você pode inspecionar e editar os parâmetros de pipeline selecionando o ícone de engrenagem **configurações** ao lado do título do rascunho do seu pipeline. 

![Captura de tela que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de treinamento

Quando você publica um pipeline, ele cria um ponto de extremidade de pipeline. Os pontos de extremidade do pipeline permitem que você reutilize e gerencie seus pipelines para repetição e automação. Neste exemplo, você configurou seu pipeline para treinar novamente.

1. Selecione **publicar** acima da tela do designer.
1. Selecione ou crie um ponto de extremidade de pipeline.

   > [!NOTE]
   > Você pode publicar vários pipelines em um único ponto de extremidade. Cada pipeline no ponto de extremidade recebe um número de versão, que você pode especificar ao chamar o ponto de extremidade do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Treinar novamente seu modelo

Agora que você tem um pipeline de treinamento publicado, você pode usá-lo para treinar novamente seu modelo usando novos dados. Você pode enviar execuções de um ponto de extremidade de pipeline do portal do Azure ou enviá-las programaticamente.

### <a name="submit-runs-by-using-the-designer"></a>Enviar execuções usando o designer

Use as etapas a seguir para enviar uma execução de ponto de extremidade de pipeline do designer:

1. Vá para a página **pontos de extremidade** .
1. Selecione a guia **pontos de extremidade do pipeline** .
1. Selecione o ponto de extremidade do pipeline.
1. Selecione a guia **pipelines publicados** .
1. Selecione o pipeline que você deseja executar.
1. Selecione **Enviar**.
1. Na caixa de diálogo instalação, você pode especificar um novo valor para o valor do caminho de dados de entrada. Esse valor aponta para o novo conjunto de novos.

![Captura de tela que mostra como configurar uma execução de pipeline com parâmetros no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Enviar execuções usando código

Você pode encontrar o ponto de extremidade REST de um pipeline publicado no painel Visão geral. Ao chamar o ponto de extremidade, você pode treinar novamente o pipeline publicado.

Para fazer uma chamada REST, você precisa de um cabeçalho de autenticação do tipo portador OAuth 2,0. Para obter informações sobre como configurar a autenticação para seu espaço de trabalho e fazer uma chamada REST com parâmetros, consulte [criar um pipeline de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial do designer](tutorial-designer-automobile-price-train-score.md) para treinar e implantar um modelo de regressão.
