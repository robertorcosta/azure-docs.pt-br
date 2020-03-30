---
title: Ajustar os hiperparâmetros de modelo
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Tune Model Hyperparameters no Azure Machine Learning para realizar uma varredura de parâmetros em um modelo para determinar as configurações ideais de parâmetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064171"
---
# <a name="tune-model-hyperparameters"></a>Ajustar os hiperparâmetros de modelo

Este artigo descreve como usar o módulo Tune Model Hyperparameters no azure Machine Learning designer (visualização). O objetivo é determinar os hiperparâmetros ideais para um modelo de aprendizagem de máquina. O módulo constrói e testa vários modelos usando diferentes combinações de configurações. Ele compara métricas em todos os modelos para obter as combinações de configurações. 

Os termos *parâmetro* e *hiperparâmetro* podem ser confusos. Os *parâmetros* do modelo são o que você define no painel direito do módulo. Basicamente, este módulo executa uma *varredura de parâmetro* sobre as configurações de parâmetro especificadas. Ele aprende um conjunto ideal de _hiperparâmetros,_ que podem ser diferentes para cada árvore de decisão específica, conjunto de dados ou método de regressão. O processo de encontrar a configuração ideal às vezes é chamado *de ajuste*. 

O módulo suporta o seguinte método para encontrar as configurações ideais para um modelo: *trem integrado e sintonia.* Neste método, você configura um conjunto de parâmetros a serem usados. Em seguida, deixe o módulo iterar sobre várias combinações. O módulo mede a precisão até encontrar um modelo "melhor". Com a maioria dos módulos de aprendiz, você pode escolher quais parâmetros devem ser alterados durante o processo de treinamento, e quais devem permanecer fixos.

Dependendo do tempo que você quer que o processo de ajuste seja executado, você pode decidir testar exaustivamente todas as combinações. Ou você pode encurtar o processo estabelecendo uma grade de combinações de parâmetros e testando um subconjunto randomizado da grade de parâmetros.

Este método gera um modelo treinado que você pode economizar para reutilização.  

> [!TIP] 
> Você pode fazer uma tarefa relacionada. Antes de começar a sintonizar, aplique a seleção de recursos para determinar as colunas ou variáveis que possuem o maior valor de informação.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Como configurar hiperparâmetros do modelo tune  

Aprender os hiperparâmetros ideais para um modelo de aprendizagem de máquina requer um uso considerável de pipelines.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Treine um modelo usando uma varredura de parâmetros  

Esta seção descreve como realizar uma varredura de parâmetros básicos, que treina um modelo usando o módulo Tune Model Hyperparameters.

1.  Adicione o módulo Tune Model Hyperparameters ao seu pipeline no designer.

2.  Conecte um modelo não treinado à entrada mais à esquerda. 



4.  Adicione o conjunto de dados que você deseja usar para treinamento e conecte-o à entrada média do Tune Model Hyperparameters.  

    Opcionalmente, se você tiver um conjunto de dados marcado, você pode conectá-lo à porta de entrada mais correta **(Conjunto de dados de validação opcional).** Isso permite medir a precisão durante o treinamento e a sintonia.

5.  No painel direito do Tune Model Hyperparameters, escolha um valor para o **modo de varredura de parâmetros**. Esta opção controla como os parâmetros são selecionados.

    - **Grade inteira**: Quando você seleciona esta opção, o módulo faz loops sobre uma grade predefinida pelo sistema, para experimentar diferentes combinações e identificar o melhor aluno. Esta opção é útil quando você não sabe quais são as melhores configurações de parâmetros e deseja experimentar todas as combinações possíveis de valores.

    - **Varredura aleatória**: Quando você selecionar esta opção, o módulo selecionará aleatoriamente valores de parâmetro satisfatoriamente em um intervalo definido pelo sistema. Você deve especificar o número máximo de execuçãos que deseja que o módulo execute. Essa opção é útil quando você deseja aumentar o desempenho do modelo usando as métricas de sua escolha, mas ainda conservar recursos de computação.    

6.  Para **a coluna Rótulo,** abra o seletor de coluna para escolher uma única coluna de rótulo.

7.  Escolha o número de corridas:

    1. **Número máximo de corridas em varredura aleatória**: Se você escolher uma varredura aleatória, você pode especificar quantas vezes o modelo deve ser treinado, usando uma combinação aleatória de valores de parâmetros.

8.  Para **o Ranking,** escolha uma única métrica para usar para classificar os modelos.

    Quando você executa uma varredura de parâmetros, o módulo calcula todas as métricas aplicáveis para o tipo de modelo e as retorna no relatório **de resultados de Sweep.** O módulo utiliza métricas separadas para modelos de regressão e classificação.

    No entanto, a métrica escolhida determina como os modelos são classificados. Apenas o modelo superior, conforme classificado pela métrica escolhida, é a saída como um modelo treinado para usar para pontuação.

9.  Para **sementes aleatórias,** digite um número a ser usado para iniciar a varredura do parâmetro. 

10. Envie o oleoduto.

## <a name="results-of-hyperparameter-tuning"></a>Resultados da sintonia hiperparâmetro

Quando o treinamento estiver completo:

+ Para exibir um conjunto de métricas de precisão para o melhor modelo, clique com o botão direito do mouse no módulo e, em seguida, selecione **Visualizar**.

    A saída inclui todas as métricas de precisão que se aplicam ao tipo de modelo, mas a métrica que você selecionou para o ranking determina qual modelo é considerado "melhor".

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.


## <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes e dicas de implementação.

### <a name="how-a-parameter-sweep-works"></a>Como funciona uma varredura de parâmetros

Quando você configura uma varredura de parâmetros, você define o escopo de sua pesquisa. A pesquisa pode usar um número finito de parâmetros selecionados aleatoriamente. Ou pode ser uma busca exaustiva sobre um espaço de parâmetro que você define.

+ **Varredura aleatória**: Esta opção treina um modelo usando um número definido de iterações. 

  Você especifica uma série de valores para iterar, e o módulo usa um subconjunto escolhido aleatoriamente desses valores. Os valores são escolhidos com substituição, o que significa que os números previamente escolhidos aleatoriamente não são removidos do pool de números disponíveis. Assim, a chance de qualquer valor ser selecionado permanece a mesma em todos os passes.  

+ **Grade inteira**: A opção de usar toda a grade significa que cada combinação é testada. Esta opção é a mais completa, mas requer mais tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controlando o comprimento e a complexidade do treinamento

A iteração em muitas combinações de configurações pode ser demorada, de modo que o módulo fornece várias maneiras de restringir o processo:

+ Limitar o número de iterações utilizadas para testar um modelo.
+ Limite o espaço dos parâmetros.
+ Limitar tanto o número de iterações quanto o espaço dos parâmetros.

Recomendamos que você pipeline com as configurações para determinar o método mais eficiente de treinamento em um determinado conjunto de dados e modelo.

### <a name="choosing-an-evaluation-metric"></a>Escolhendo uma métrica de avaliação

Ao final dos testes, o modelo apresenta um relatório que contém a precisão de cada modelo para que você possa revisar os resultados métricos:

- Um conjunto uniforme de métricas é usado para todos os modelos de classificação binária.
- A precisão é usada para todos os modelos de classificação de várias classes.
- Um conjunto diferente de métricas é usado para modelos de regressão. 

No entanto, durante o treinamento, você deve escolher uma *única* métrica para usar no ranking dos modelos gerados durante o processo de ajuste. Você pode achar que a melhor métrica varia, dependendo do problema do seu negócio e do custo de falsos positivos e falsos negativos.

#### <a name="metrics-used-for-binary-classification"></a>Métricas utilizadas para classificação binária

-   **Precisão** é a proporção de resultados verdadeiros ao total de casos.  

-   **Precisão** é a proporção de resultados verdadeiros para resultados positivos.  

-   **Recall** é a fração de todos os resultados corretos sobre todos os resultados.  

-   **F-score** é uma medida que equilibra precisão e recordação.  

-   **AUC** é um valor que representa a área a curva quando falsos positivos são plotados no eixo x e verdadeiros positivos são plotados no eixo y.  

-   **Perda média de log** é a diferença entre duas distribuições de probabilidade: a verdadeira e a do modelo.  

#### <a name="metrics-used-for-regression"></a>Métricas utilizadas para regressão

-   **Erro absoluto médio** média silude todos os erros do modelo, onde o *erro* significa a distância do valor previsto do valor real. É muitas vezes abreviado como *MAE.*  

-   **Raiz de erro quadrado médio** mede a média dos quadrados dos erros e, em seguida, toma a raiz desse valor. É muitas vezes abreviado como *RMSE*.  

-   **Erro absoluto relativo** representa o erro como uma porcentagem do valor real.  

-   **O erro quadrado relativo** normaliza o erro total quadrado, dividindo-se pelo erro total quadrado dos valores previstos.  

-   **Coeficiente de determinação** é um único número que indica o quão bem os dados se encaixam em um modelo. Um valor de um significa que o modelo corresponde exatamente aos dados. Um valor de zero significa que os dados são aleatórios ou não podem ser adequados ao modelo. É muitas vezes chamado *r<sup>2</sup>*, *R<sup>2</sup>*, ou *r-quadrado*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Módulos que não suportam uma varredura de parâmetros

Quase todos os alunos do Azure Machine Learning suportam a validação cruzada com uma varredura integrada de parâmetros, que permite que você escolha os parâmetros para pipeline. Se o aluno não suportar a definição de uma gama de valores, você ainda pode usá-lo em validação cruzada. Neste caso, uma série de valores permitidos é selecionada para a varredura. 


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

