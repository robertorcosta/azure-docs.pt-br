---
title: 'Regressão Linear: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão linear no Azure Machine Learning para criar um modelo de regressão linear para uso em um pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477554"
---
# <a name="linear-regression-module"></a>Módulo de Regressão Linear
Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de regressão linear para uso em um pipeline.  A regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um desfecho numérico, ou variável dependente. 

Você usa este módulo para definir um método de regressão linear e, em seguida, treinar um modelo usando um conjunto de dados rotulado. O modelo treinado pode então ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

A regressão linear é um método estatístico comum, que tem sido adotado no aprendizado de máquina e aprimorado com muitos novos métodos para encaixar a linha e medir o erro. No sentido mais básico, a regressão refere-se à previsão de um alvo numérico. A regressão linear ainda é uma boa escolha quando você quer um modelo simples para uma tarefa preditiva básica. A regressão linear também tende a funcionar bem em conjuntos de dados esparsos e de alta dimensão sem complexidade.

O Azure Machine Learning suporta uma variedade de modelos de regressão, além da regressão linear. No entanto, o termo "regressão" pode ser interpretado de forma frouxa, e alguns tipos de regressão fornecidas em outras ferramentas não são suportados.

+ O problema clássico de regressão envolve uma única variável independente e uma variável dependente. Isso é chamado *de regressão simples.*  Este módulo suporta uma regressão simples.

+ *A regressão linear múltipla* envolve duas ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas nos quais as entradas múltiplas são usadas para prever um único resultado numérico também são chamados *de regressão linear multivariada*.

    O módulo **de Regressão Linear** pode resolver esses problemas, assim como a maioria dos outros módulos de regressão.

+ *A regressão multi-rótulo* é a tarefa de prever múltiplas variáveis dependentes dentro de um único modelo. Por exemplo, na regressão logística de multi-rótulo, um exemplo pode ser atribuído para vários rótulos diferentes. (Isso é diferente da tarefa de prever vários níveis dentro de uma única variável de classe.)

    Esse tipo de regressão não é suportado no Azure Machine Learning. Para prever várias variáveis, crie um aluno separado para cada saída que você deseja prever.

Há anos os estatísticos vêm desenvolvendo métodos cada vez mais avançados de regressão. Isso é verdade até mesmo para a regressão linear. Este módulo suporta dois métodos para medir o erro e encaixar a linha de regressão: método de menos quadrados comum e descida gradiente.

- **A descida do gradiente** é um método que minimiza a quantidade de erro em cada etapa do processo de treinamento do modelo. Há muitas variações do espaço descendente do gradiente e a otimização para vários problemas de aprendizado tem sido muito estudada. Se você escolher esta opção para **o método Solution,** você pode definir uma variedade de parâmetros para controlar o tamanho da etapa, a taxa de aprendizado e assim por diante. Esta opção também suporta o uso de uma varredura de parâmetros integrados.

- **Os quadrados menos comuns** são uma das técnicas mais utilizadas na regressão linear. Por exemplo, menos quadrados é o método que é usado no Analysis Toolpak para Microsoft Excel.

    Os quadrados mínimos comuns se referem à função de perda, que calcula o erro como a soma do quadrado da distância entre o valor real e a linha prevista e se ajusta ao modelo, minimizando o erro ao quadrado. Este método assume uma forte relação linear entre os insumos e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar regressão linear

Este módulo suporta dois métodos para encaixar um modelo de regressão, com diferentes opções:

+ [Ajuste um modelo de regressão usando quadrados menos comuns](#create-a-regression-model-using-ordinary-least-squares)

    Para pequenos conjuntos de dados, é melhor selecionar quadrados menos comuns. Isso deve dar resultados semelhantes ao Excel.
    
+ [Crie um modelo de regressão usando descida de gradiente on-line](#create-a-regression-model-using-online-gradient-descent)

    O espaço descendente do gradiente é uma função de perda melhor para modelos que são mais complexos, ou que têm muito poucos dados de treinamento, dado o número de variáveis.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Crie um modelo de regressão usando quadrados menores comuns

1. Adicione o módulo **Modelo de Regressão Linear** ao seu pipeline no designer.

    Você pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Modelo de Inicialização,** **expanda a regressão**e arraste o módulo **Modelo de Regressão Linear** para o seu pipeline.

2. No painel **Propriedades,** na lista de paradas do **método Solução,** selecione **'Quadrados Mínimos Comuns '** Esta opção especifica o método de computação usado para encontrar a linha de regressão.

3. No **peso de regularização L2,** digite o valor a ser usado como peso para regularização L2. Recomendamos que você use um valor não-zero para evitar a supermontagem.

     Para saber mais sobre como a regularização afeta a adequação do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecione a **opção, Inclua o termo intercepte,** se quiser visualizar o termo para interceptar.

    Desmarque esta opção se você não precisar rever a fórmula de regressão.

5. Para **sementes de número aleatório,** você pode digitar opcionalmente um valor para semear o gerador de números aleatórios usado pelo modelo.

    Usar um valor de semente é útil se você quiser manter os mesmos resultados em diferentes corridas do mesmo pipeline. Caso contrário, o padrão é usar um valor do relógio do sistema.


7. Adicione o módulo [Modelo de Trem](./train-model.md) ao seu pipeline e conecte um conjunto de dados rotulado.

8. Envie o oleoduto.

### <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo de quadrados menos comuns

Após o treinamento ser concluído:


+ Para fazer previsões, conecte o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com um conjunto de dados de novos valores. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Crie um modelo de regressão usando descida de gradiente on-line

1. Adicione o módulo **Modelo de Regressão Linear** ao seu pipeline no designer.

    Você pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Modelo de Inicialização,** **expanda a regressão**e arraste o módulo **Modelo de Regressão Linear** para o seu pipeline

2. No painel **Propriedades,** na lista de paradas do **método Solução,** escolha **Descida de Gradiente Online** como o método de computação usado para encontrar a linha de regressão.

3. Para **criar o modo treinador,** indique se deseja treinar o modelo com um conjunto predefinido de parâmetros ou se deseja otimizar o modelo usando uma varredura de parâmetros.

    + **Parâmetro Único**: Se você sabe como deseja configurar a rede de regressão linear, você pode fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.  

   
4. Para **taxa de aprendizagem,** especifique a taxa inicial de aprendizado para o otimizador de descida de gradiente estocástico.

5. Para **Número de épocas de treinamento, digite**um valor que indique quantas vezes o algoritmo deve iterar através de exemplos. Para conjuntos de dados com um pequeno número de exemplos, esse número deverá ser grande para alcançar convergência.

6. **Normalize recursos**: Se você já normalizou os dados numéricos usados para treinar o modelo, você pode desmarcar esta opção. Por padrão, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização a novos dados usados para pontuação.

7. No **peso de regularização L2,** digite o valor a ser usado como peso para regularização L2. Recomendamos que você use um valor não-zero para evitar a supermontagem.

    Para saber mais sobre como a regularização afeta a adequação do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecione a opção, Diminua a **taxa de aprendizado,** se você quiser que a taxa de aprendizado diminua à medida que as iterações progridem.  

10. Para **sementes de número aleatório,** você pode digitar opcionalmente um valor para semear o gerador de números aleatórios usado pelo modelo. Usar um valor de semente é útil se você quiser manter os mesmos resultados em diferentes corridas do mesmo pipeline.


12. Adicione um conjunto de dados rotulado e um dos módulos de treinamento.

    Se você não estiver usando uma varredura de parâmetros, use o módulo [Modelo de Trem.](train-model.md)

13. Envie o oleoduto.

### <a name="results-for-online-gradient-descent"></a>Resultados para descida de gradiente on-line

Após o treinamento ser concluído:

+ Para fazer previsões, conecte o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 