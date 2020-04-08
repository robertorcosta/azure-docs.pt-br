---
title: Retreinar modelos usando o azure Machine Learning designer (visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a retreinar modelos com pipelines publicados no azure Machine Learning designer (preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810366"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Readaptar modelos com o designer do Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de como fazer, você aprende a usar o designer de Machine Learning do Azure para retreinar um modelo de aprendizado de máquina. Você usará pipelines publicados para automatizar seu fluxo de trabalho e definir parâmetros para treinar seu modelo em novos dados. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de tubulação.
> * Publique seu oleoduto de treinamento.
> * Retreine seu modelo com novos parâmetros.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace do Azure Machine Learning com o SKU Enterprise.
* Um conjunto de dados acessível ao designer. Pode ser um dos seguintes:
   * Um conjunto de dados registrado do Azure Machine Learning
    
     **Ou...**
   * Um arquivo de dados armazenado em um armazenamento de dados azure Machine Learning.
   
Para obter informações sobre acesso a dados usando o designer, [consulte Como importar dados para o designer](how-to-designer-import-data.md).

Este artigo também pressupõe que você tem conhecimento básico de construção de pipelines no designer. Para uma introdução guiada, complete o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline utilizado neste artigo é uma versão alterada da [Amostra 3: Previsão de Renda](samples-designer.md#classification-samples). O pipeline usa o módulo [Importar dados](algorithm-module-reference/import-data.md) em vez do conjunto de dados de amostra para mostrar como treinar modelos usando seus próprios dados.

![Captura de tela que mostra o pipeline de amostra modificado com uma caixa destacando o módulo Dados de Importação](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Crie um parâmetro de pipeline

Crie parâmetros de pipeline para definir dinamicamente variáveis em tempo de execução. Para este exemplo, você mudará o caminho de dados de treinamento de um valor fixo para um parâmetro, para que você possa retreinar seu modelo em diferentes dados.

1. Selecione o módulo **Dados de importação.**

    > [!NOTE]
    > Este exemplo usa o módulo Importar dados para acessar dados em um datastore registrado. No entanto, você pode seguir etapas semelhantes se usar padrões alternativos de acesso a dados.

1. No painel de detalhes do módulo, à direita da tela, selecione sua fonte de dados.

1. Digite o caminho para seus dados. Você também pode selecionar **'Procurar' caminho** para navegar na árvore de arquivos. 

1. Passe o mouse sobre o campo **Caminho** e selecione as elipses acima do campo **Caminho** que aparecem.

    ![Captura de tela que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecione **Adicionar ao parâmetro de pipeline**.

1. Forneça um nome de parâmetro e um valor padrão.

   > [!NOTE]
   > Você pode inspecionar e editar os parâmetros do pipeline selecionando o ícone de engrenagem **Configurações** ao lado do título do rascunho do pipeline. 

1. Clique em **Salvar**.

1. Envie a corrida do oleoduto.

## <a name="find-a-trained-model"></a>Encontre um modelo treinado

O designer salva toda a saída do pipeline, incluindo modelos treinados, para a conta padrão de armazenamento do espaço de trabalho. Você também pode acessar modelos treinados diretamente no designer:

1. Espere o oleoduto terminar de funcionar.
1. Selecione o módulo **Treinar Modelo**.
1. No painel de detalhes do módulo, à direita da tela, selecione **Saídas + logs**.
1. Você pode encontrar seu modelo em **Outras saídas,** juntamente com logs de execução.
1. Alternativamente, selecione o ícone **Exibir saída.** A partir daqui, você pode seguir as instruções na caixa de diálogo para navegar diretamente para o seu datastore. 

![Captura de tela que mostra como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de treinamento

Publique um pipeline para um ponto final de pipeline para reutilizar facilmente seus gasodutos no futuro. Um ponto final do pipeline cria um ponto final REST para invocar pipeline no futuro. Neste exemplo, o ponto final do pipeline permite que você reutilize seu pipeline para retreinar um modelo em diferentes dados.

1. Selecione **Publicar** acima da tela do designer.
1. Selecione ou crie um ponto final do pipeline.

   > [!NOTE]
   > Você pode publicar vários pipelines em um único ponto final. Cada pipeline em um determinado ponto final recebe um número de versão, que você pode especificar quando você chamar o ponto final do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Retreinar seu modelo

Agora que você tem um pipeline de treinamento publicado, você pode usá-lo para retreinar seu modelo em novos dados. Você pode enviar corridas a partir de um ponto final do pipeline a partir do espaço de trabalho do estúdio ou programáticamente.

### <a name="submit-runs-by-using-the-designer"></a>Enviar executa usando o designer

Use as seguintes etapas para enviar um ponto final de pipeline parametrizado executado a partir do designer:

1. Vá para a página **Endpoints** em seu espaço de trabalho do estúdio.
1. Selecione a guia **Pontos finais pipeline.** Em seguida, selecione o ponto final do pipeline.
1. Selecione a guia **''Pipelines'** Publicado. Em seguida, selecione a versão de pipeline que deseja executar.
1. Selecione **Enviar**.
1. Na caixa de diálogo configuração, você pode especificar os valores de parâmetros para a execução. Para este exemplo, atualize o caminho de dados para treinar seu modelo usando um conjunto de dados não-americano.

![Captura de tela que mostra como configurar um pipeline parametrizado executado no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Enviar executa usando código

Você pode encontrar o ponto final REST de um pipeline publicado no painel de visão geral. Ao chamar o ponto final, você pode retreinar o pipeline publicado.

Para fazer uma chamada REST, você precisa de um cabeçalho de autenticação tipo OAuth 2.0. Para obter informações sobre como configurar a autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada, consulte [Construir um pipeline de Aprendizado de Máquina azure para pontuação em lote](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar um ponto final de pipeline de treinamento parametrizado usando o designer.

Para um passo a passo completo de como você pode implantar um modelo para fazer previsões, consulte o tutorial do [designer](tutorial-designer-automobile-price-train-score.md) para treinar e implantar um modelo de regressão.
