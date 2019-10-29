---
title: 'Tutorial: Prever o preço de automóveis com a interface visual'
titleSuffix: Azure Machine Learning
description: Saiba como treinar, pontuar e implantar um modelo de machine learning usando uma interface visual do tipo "arrastar e soltar". Este tutorial é a primeira parte de uma série de duas partes sobre a previsão de preços de automóveis usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 3852531615418ffe5397295bc194de34139d6e81
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792640"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço de automóveis com a interface visual

Neste tutorial de duas partes, você aprende a usar a interface visual do Azure Machine Learning para desenvolver e implantar uma solução analítica preditiva que prevê o preço de qualquer carro. 

Na parte um, você configura o ambiente, arrasta e solta módulos em uma tela interativa e os conecta um ao outro para criar um pipeline do Azure Machine Learning.

Na primeira parte do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um novo pipeline
> * Importar dados
> * Preparar dados
> * Treinar um modelo de machine learning
> * Avaliar um modelo de machine learning

Na [parte dois](ui-tutorial-automobile-price-deploy.md) do tutorial, você aprende a implantar seu modelo preditivo como um serviço Web do Azure para prever o preço de qualquer carro com base nas especificações técnicas enviadas. 

> [!Note]
>Uma versão concluída deste tutorial está disponível como um pipeline de exemplo.
>
>Para encontrá-lo, vá para a **Interface visual em seu workspace**. Na seção **Novo pipeline**, selecione **Amostra 1 – Regressão: Previsão de Preços de Automóveis (Básica)** .

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

Os pipelines do Azure Machine Learning organizam várias etapas dependentes do aprendizado de máquina e do processamento de dados em um único recurso. Os pipelines ajudam você a organizar, gerenciar e reutilizar fluxos de trabalho de aprendizado de máquina complexos entre projetos e usuários. Para criar um pipeline do Azure Machine Learning, você precisa de um workspace do serviço do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar um novo workspace

Se você tiver um workspace do Serviço do Azure Machine Learning, passe para a próxima seção.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre em [ml.azure.com](https://ml.azure.com) e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Interface Visual**.

    ![Captura de tela do workspace visual mostrando como acessar a interface visual](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Selecione **Módulos predefinidos e fáceis de usar**.

1. Selecione o nome do pipeline padrão **"Pipeline criado em ..."** na parte superior da tela e renomeie-o para algo significativo. Por exemplo, **"Previsão de preços de automóveis"** . O nome não precisa ser exclusivo.

## <a name="import-data"></a>Importar dados

Há várias amostras de conjuntos de incluídas na interface visual para que você possa fazer experimentos. Neste tutorial, use o conjunto de dados de exemplo **Dados de preços de automóveis (brutos)** . 

1. À esquerda da tela do pipeline há uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de Dados** e, em seguida, selecione a seção **Exemplos** para exibir os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados, **Dados de preço de automóvel (brutos)** e arraste-o para a tela.

   ![Arraste os dados para a tela](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualizar os dados

Você pode visualizar os dados para entender o conjunto de dados que será usado.

1. Selecione o módulo **Dados de preço de automóvel (brutos)** .

1. No painel **Propriedades** à direita da tela, selecione **Saídas**.

1. Selecione o ícone de grafo para visualizar os dados.

    ![Visualizar os dados](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Selecione as diferentes colunas na janela de dados para exibir informações sobre cada um.

    Cada linha representa um automóvel e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas nesse conjunto de dados.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de dados normalmente exigem algum pré-processamento antes da análise. Talvez você tenha observado alguns valores ausentes ao visualizar o conjunto de dados. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente. Você removerá colunas com muitos valores ausentes e removerá linhas individuais que tiverem valores ausentes.

### <a name="remove-a-column"></a>Remover uma coluna

Quando treina um modelo, você precisa fazer algo sobre os dados que estão faltando. Neste conjunto de dados, a coluna **normalized-losses** tem muitos valores ausentes; portanto, você a excluirá do modelo completamente.

1. Selecione com quais colunas de dados você deseja trabalhar. Digite **Selecionar** na caixa de Pesquisa, na parte superior da paleta, para encontrar o módulo **Selecionar colunas no conjunto de dados**.

1. Clique e arraste no módulo **Selecionar colunas no conjunto de dados** para a tela. Solte o módulo embaixo do módulo do conjunto de dados.

1. Conecte o conjunto de dados que você adicionou anteriormente ao módulo **Selecionar colunas no conjunto de dados** clicando e arrastando. Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, até a porta de entrada do módulo **Selecionar colunas no conjunto de dados**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Crie um fluxo de dados por meio do seu pipeline quando você conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Conectar módulos](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel **Propriedades** à direita da tela, selecione **Parâmetros** > **Editar coluna**.

1. Selecione o **+** para adicionar uma nova regra.

1. No menu suspenso, selecione **Excluir** e **Nomes de coluna**.
    
1. Digite **normalized-losses** na caixa de texto.

1. No canto inferior direito, selecione **Salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    O painel de propriedades mostra que a coluna **normalized-losses** foi excluída.

1. Selecione o módulo **Selecionar colunas no conjunto de dados**. 

1. Nas **Propriedades**, selecione **Parâmetros** > **Comentário** e insira "Excluir perdas normalizadas.".

### <a name="clean-missing-data"></a>Limpar dados ausentes

Seu conjunto de dados ainda tem valores ausentes após a remoção da coluna **normalized-losses**. Você pode remover os dados ausentes restantes usando o módulo **Limpar Dados Ausentes**.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos da interface visual.

1. Digite **Limpar** na caixa de pesquisa para encontrar o módulo **Limpar Dados Ausentes**.

1. Arraste o módulo **Limpar Dados Ausentes** para a tela do pipeline e conecte-o ao módulo **Selecionar Colunas no Conjunto de Dados**. 

1. No painel Propriedades, selecione **Remover linha inteira** em **Modo de limpeza**.

1. No painel Propriedades, digite "Remover linhas com valores ausentes". na caixa **Comentário**.  

    Agora, seu pipeline deve ser semelhante ao seguinte:
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Agora que os dados foram pré-processados, é possível construir um modelo preditivo. Você usará seus dados para treinar o modelo. Em seguida, você testará o modelo para ver o nível de precisão com o qual ele prevê os preços.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

**Classificação** e **regressão** são dois tipos de técnicas de algoritmo de machine learning supervisionado. A **classificação** prevê uma resposta com base em um conjunto definido de categorias, como uma cor (vermelho, azul ou verde). A **regressão** é usada para prever um número.

Como você deseja prever o preço, que é um número, use um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

Use seus dados para treinar o modelo e testá-lo, dividindo os dados em dois conjuntos de dados separados.

1. Digite **dividir dados** na caixa de pesquisa para encontrar o módulo **Dividir Dados** e conectá-lo à porta esquerda do módulo **Limpar Dados Ausentes**.

1. Selecione o módulo **Dividir dados**.

1. No painel Propriedades, defina a **Fração de linhas no primeiro conjunto de dados de saída** como 0,7.

    Isso separa 70% dos dados para treinar o modelo e mantém 30% para o teste.

1. No painel Propriedades, digite "Dividir o conjunto de dados em conjunto de treinamento (0.7) e conjunto de teste (0.3)". na caixa **Comentário**.

### <a name="train-the-model"></a>Treinar o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclua o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço.

1. Para selecionar o algoritmo de aprendizado, limpe a caixa de pesquisa da paleta de módulos.

1. Expanda **Algoritmos de Machine Learning**.
    
    Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina.

1. Para este pipeline, selecione **Regressão** > **Regressão Linear** e arraste-a para a tela do pipeline.

1. Encontre e arraste o módulo **Treinar Modelo** para a tela do pipeline. 

1. Conecte a saída do módulo Regressão Linear à entrada esquerda do módulo Treinar Modelo.

1. Conecte-se a saída dos dados de treinamento (porta esquerda) do módulo **Dividir Dados** à entrada à direita do módulo **Treinar Modelo**.

    ![Captura de tela mostrando a configuração correta do módulo Treinar Modelo. O módulo Regressão Linear se conecta à porta esquerda do módulo Treinar Modelo e o módulo Dividir Dados se conecta à porta direita do módulo Treinar Modelo](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Selecione o módulo **Treinar Modelo**.

1. No painel Propriedades, clique no seletor **Editar coluna**.

1. Na caixa de diálogo **Coluna de Rótulos**, expanda o menu suspenso e selecione **Nomes de coluna**. Na caixa de texto, digite **preço**. O preço é o valor que o modelo vai prever.

    Seu pipeline deve ter esta aparência:

    ![Captura de tela mostrando a configuração correta do pipeline após a adição do módulo Treinar Modelo.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de machine learning

Agora que você treinou o modelo usando 70% de seus dados, use-o para pontuar os outros 30% dos dados e ver se o modelo funciona bem.

1. Digite **pontuar modelo** na caixa de pesquisa para encontrar o módulo **Pontuar Modelo** e arraste o módulo para a tela do pipeline. 

1. Conecte a saída do módulo **Treinar Modelo** à porta de entrada esquerda do módulo **Pontuar Modelo**. Conecte a saída de dados de teste (porta direita) do módulo **Dividir Dados** à porta de entrada direita do módulo **Pontuar Modelo**.

1. Digite **avaliar** na caixa de pesquisa para encontrar **Avaliar Modelo** e arraste o módulo para a tela do pipeline. 

1. Conecte a saída do módulo **Pontuar Modelo** à entrada esquerda do módulo **Avaliar Modelo**. 

    O pipeline final deve ser semelhante ao seguinte:

    ![Captura de tela mostrando a configuração correta do pipeline.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Executar o pipeline

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Exibir resultados

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. 

1. Exiba a saída do módulo **Pontuar Modelo** selecionando o módulo **Pontuar Modelo**.

1. No painel **Propriedades**, selecione **Saídas** > **Visualizar**. A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna "Rótulo Pontuado"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Exiba a saída do módulo **Avaliar Modelo** selecionando o módulo **Pontuar Modelo**.

1. No painel **Propriedades**, selecione **Saída** > **Visualizar** e, em seguida, selecione **Visualizar**.

As seguintes estatísticas são mostradas para o modelo:

* **MAE (Média de Erros Absolutos)** : A média de erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **RMSE (Raiz Quadrada da Média de Erros Quadrados)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
* **Erro absoluto relativo**: a média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado relativo**: a média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o valor de R-quadrado, essa é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para Coeficiente de Determinação, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Na primeira parte deste tutorial, você concluiu estas etapas:

* Criar um pipeline
* Preparar os dados
* Treinar o modelo
* Pontuar e avaliar o modelo

Na segunda parte, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantação de modelos](ui-tutorial-automobile-price-deploy.md)
