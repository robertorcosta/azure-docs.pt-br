---
title: 'Normalizar dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Normalize Dados no Azure Machine Learning para transformar um conjunto de dados através da *normalização*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477520"
---
# <a name="normalize-data-module"></a>Normalizar módulo de dados

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para transformar um conjunto de dados através da *normalização*.

Normalização é uma técnica frequentemente aplicada como parte da preparação de dados para aprendizado de máquina. O objetivo da normalização é alterar os valores das colunas numéricas no conjunto de dados para usar uma escala comum, sem distorcer diferenças nas faixas de valores ou perder informações. A normalização também é necessária para que alguns algoritmos modelem os dados corretamente.

Por exemplo, suponha que seu conjunto de dados de entrada contenha uma coluna com valores que variam de 0 a 1 e outra coluna com valores que variam de 10.000 a 100.000. A grande diferença na *escala* dos números pode causar problemas quando você tenta combinar os valores como características durante a modelagem.

*A normalização* evita esses problemas criando novos valores que mantêm a distribuição geral e as proporções nos dados de origem, mantendo os valores dentro de uma escala aplicada em todas as colunas numéricas utilizadas no modelo.

Este módulo oferece várias opções para transformar dados numéricos:

- Você pode alterar todos os valores para uma escala 0-1, ou transformar os valores representando-os como classificações percentis em vez de valores absolutos.
- Você pode aplicar a normalização para uma única coluna ou em várias colunas em um mesmo conjunto de dados.
- Se você precisar repetir o pipeline ou aplicar as mesmas etapas de normalização a outros dados, você pode salvar as etapas como uma transformação de normalização e aplicá-la a outros conjuntos de dados que tenham o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos executam o dimensionamento ou normalização dos seus próprios dados. Portanto, quando você escolher um algoritmo de aprendizagem de máquina para usar na construção de um modelo preditivo, certifique-se de rever os requisitos de dados do algoritmo antes de aplicar a normalização aos dados de treinamento.

##  <a name="configure-normalize-data"></a>Configurar dados de normalização

Você pode aplicar apenas um método de normalização por vez usando este módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas selecionadas. Para usar diferentes métodos de normalização, use uma segunda instância de **Normalize Data**.

1. Adicione o módulo **Normalize Dados** ao seu pipeline. Você pode encontrar o módulo In Azure Machine Learning, em **Transformação de Dados,** na categoria **Escala e Redução.**

2. Conecte um conjunto de dados que contenha pelo menos uma coluna de todos os números.

3. Use o Seletor de colunas para escolher as colunas numéricas para normalizar. Se você não escolher colunas individuais, por padrão **todas as** colunas numéricas de tipo na entrada serão incluídas e o mesmo processo de normalização será aplicado a todas as colunas selecionadas. 

    Isso pode levar a resultados estranhos se você incluir colunas numéricas que não devem ser normalizadas! Verifique sempre as colunas com cuidado.

    Se não fordetectada colunas numéricas, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico suportado.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico sejam fornecidas como entrada, tente usar as [Colunas selecionadas no](./select-columns-in-dataset.md) módulo Dataset antes **de Normalizar dados**.

4. **Use 0 para colunas constantes quando verificada**: Selecione esta opção quando qualquer coluna numérica contiver um único valor imutável. Isso garante que essas colunas não sejam usadas em operações de normalização.

5. Na lista de isento do **método Detransformação,** escolha uma única função matemática para aplicar em todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores em uma pontuação z.
    
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando pontuações z&#45;](media/module/aml-normalization-z-score.png)
  
      A média e o desvio padrão são calculados para cada coluna separadamente. O desvio padrão da população é usado.
  
    - **MinMax**: O normalizador min-max redimensiona linearmente cada recurso para o intervalo [0,1].
    
      O redimensionamento do intervalo [0,1] é feito mudando os valores de cada recurso para que o valor mínimo seja 0 e, em seguida, dividindo pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando a função min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logística**: Os valores na coluna são transformados utilizando a seguinte fórmula:

      ![fórmula de normalização pela função de logística](media/module/aml-normalization-logistic.png "AML_normalization logística")  
  
    - **LogNormal**: Esta opção converte todos os valores em uma escala lognormal.
  
      Os valores na coluna são transformados usando a seguinte fórmula:
  
      ![registro de fórmula&#45;distribuição normal](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Aqui μ e σ são os parâmetros da distribuição, computados empiricamente a partir dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH**: Todos os valores são convertidos em uma tangente hiperbólica.
    
      Os valores na coluna são transformados usando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Envie o pipeline ou clique duas vezes no módulo **Normalizar dados** e selecione **Executar selecionado**. 

## <a name="results"></a>Resultados

O módulo **Normalize Data** gera duas saídas:

- Para visualizar os valores transformados, clique com o botão direito do mouse no módulo e selecione **Visualizar**.

    Por padrão, os valores são transformados no lugar. Se você quiser comparar os valores transformados com os valores originais, use o módulo [Adicionar colunas](./add-columns.md) para recombinar os conjuntos de dados e visualizar as colunas lado a lado.

- Para salvar a transformação para que você possa aplicar o mesmo método de normalização a outro conjunto de dados, selecione o módulo e selecione **Registrar conjunto de dados** na guia **Saídas** no painel direito.

    Em seguida, você pode carregar as transformações **salvas** do grupo Transformações do painel de navegação à esquerda e aplicá-lo a um conjunto de dados com o mesmo esquema usando [Aplicar Transformação](apply-transformation.md).  


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 