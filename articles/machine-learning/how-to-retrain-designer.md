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
ms.date: 02/24/2020
ms.openlocfilehash: c8791e933882832dc7b0037c860a4c4e1e9a54c7
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389028"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Readaptar modelos com o designer do Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de como fazer, você aprende a usar o designer de Machine Learning do Azure para retreinar um modelo de aprendizado de máquina. Descubra como usar pipelines publicados para automatizar fluxos de trabalho de aprendizado de máquina para retreinamento.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de tubulação.
> * Publique seu oleoduto de treinamento.
> * Retreine seu modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).
* Um espaço de trabalho de aprendizado de máquina do Azure com o Enterprise SKU.

Este artigo pressupõe que você tem conhecimento básico de construção de dutos no designer. Para obter uma introdução guiada ao designer, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline utilizado neste artigo é uma versão alterada da encontrada na [Amostra 3: Previsão de Renda](how-to-designer-sample-classification-predict-income.md). Ele usa o módulo [Importar dados](algorithm-module-reference/import-data.md) em vez do conjunto de dados de amostra para mostrar como treinar um modelo usando seus próprios dados.

![Captura de tela que mostra o pipeline de amostra modificado com uma caixa destacando o módulo Dados de Importação](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Para retreinar um modelo, você precisa de um modelo inicial. Nesta seção, você aprende a treinar um modelo e acessar o modelo salvo usando o designer.

1. Selecione o módulo **Dados de importação.**
1. No painel de propriedades, especifique uma fonte de dados.

   ![Captura de tela que mostra uma configuração de amostra do módulo Importar dados](./media/how-to-retrain-designer/import-data-settings.png)

   Para este exemplo, os dados são armazenados em um [datastore do Azure](how-to-access-data.md). Se você ainda não tiver um datastore, você pode criar um agora selecionando **Novo datastore**.

1. Especifique o caminho para seus dados. Você também pode selecionar **o caminho procurar** para navegar no seu datastore. 
1. Selecione **Enviar** na parte superior da tela.
    
   > [!NOTE]
   > Se você já tiver definido um cálculo padrão para este rascunho de pipeline, o pipeline será executado automaticamente. Caso contrário, você pode seguir as instruções no painel de configurações para definir um agora.

### <a name="find-your-trained-model"></a>Encontre seu modelo treinado

O designer salva todas as saídas de pipeline, incluindo modelos treinados, para a conta de armazenamento padrão. No entanto, você também pode acessar modelos treinados diretamente no designer:

1. Espere o oleoduto terminar de funcionar.
1. Selecione o módulo **Treinar Modelo**.
1. No painel de configurações, selecione **Saídas+logs**.
1. Selecione o ícone **Exibir saída** e siga as instruções na janela pop-up para encontrar o modelo treinado.

![Captura de tela que mostra como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Crie um parâmetro de pipeline

Adicione parâmetros de pipeline para definir dinamicamente variáveis em tempo de execução. Para este pipeline, adicione um parâmetro para o caminho de dados de treinamento para que você possa retreinar seu modelo em um novo conjunto de dados.

1. Selecione o módulo **Dados de importação.**
1. No painel de configurações, selecione as elipses acima do campo **Caminho.**
1. Selecione **Adicionar ao parâmetro de pipeline**.
1. Forneça um nome de parâmetro e um valor padrão.

   > [!NOTE]
   > Você pode inspecionar e editar os parâmetros do pipeline selecionando o ícone de engrenagem **Configurações** ao lado do título do rascunho do pipeline. 

![Captura de tela que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de treinamento

Quando você publica um pipeline, ele cria um ponto final do pipeline. Os pontos finais do pipeline permitem que você reutilize e gerencie seus pipelines para repetibilidade e automação. Neste exemplo, você configurou seu pipeline para retreinamento.

1. Selecione **Publicar** acima da tela do designer.
1. Selecione ou crie um ponto final do pipeline.

   > [!NOTE]
   > Você pode publicar vários pipelines em um único ponto final. Cada pipeline no ponto final recebe um número de versão, que você pode especificar quando você chamar o ponto final do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Retreinar seu modelo

Agora que você tem um pipeline de treinamento publicado, você pode usá-lo para retreinar seu modelo usando novos dados. Você pode enviar corridas a partir de um ponto final de pipeline a partir do portal Azure ou enviá-las programáticamente.

### <a name="submit-runs-by-using-the-designer"></a>Enviar executa usando o designer

Use as seguintes etapas para enviar um ponto final de pipeline executado a partir do designer:

1. Vá para a página **Endpoints.**
1. Selecione a guia **Pontos finais pipeline.**
1. Selecione o ponto final do pipeline.
1. Selecione a guia **''Pipelines'** Publicado.
1. Selecione o pipeline que deseja executar.
1. Selecione **Enviar**.
1. Na caixa de diálogo configuração, você pode especificar um novo valor para o valor do caminho de dados de entrada. Esse valor aponta para o seu novo conjunto de dados.

![Captura de tela que mostra como configurar um pipeline parametrizado executado no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Enviar executa usando código

Você pode encontrar o ponto final REST de um pipeline publicado no painel de visão geral. Ao chamar o ponto final, você pode retreinar o pipeline publicado.

Para fazer uma chamada REST, você precisa de um cabeçalho de autenticação tipo OAuth 2.0. Para obter informações sobre como configurar a autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada, consulte [Construir um pipeline de Aprendizado de Máquina azure para pontuação em lote](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial de designer](tutorial-designer-automobile-price-train-score.md) para treinar e implantar um modelo de regressão.
