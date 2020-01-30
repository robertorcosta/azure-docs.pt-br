---
title: Resumir dados
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo resumir dados em Azure Machine Learning para gerar um relatório de estatísticas descritivas básico para as colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: be6fd633f026c98e8f75467dc8661e695e121721
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841260"
---
# <a name="summarize-data"></a>Resumir dados

Este artigo descreve um módulo do designer de Azure Machine Learning (versão prévia).

Use o módulo resumir dados para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas de resumo são úteis quando você deseja entender as características do conjunto de todos. Por exemplo, talvez você precise saber:

- Quantos valores ausentes existem em cada coluna?
- Quantos valores exclusivos existem em uma coluna de recurso?
- Qual é a média e o desvio padrão de cada coluna?

O módulo calcula as pontuações importantes para cada coluna e retorna uma linha de estatísticas de resumo para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar dados de resumo  

1. Adicione o módulo **resumir dados** ao seu pipeline. Você pode encontrar esse módulo na categoria **funções estatísticas** no designer.

1. Conecte o conjunto de um para o qual você deseja gerar um relatório.

    Se você quiser relatar apenas algumas colunas, use o módulo [selecionar colunas no conjunto de DataSet](select-columns-in-dataset.md) para projetar um subconjunto de colunas com o qual trabalhar.

1. Nenhum parâmetro adicional é necessário. Por padrão, o módulo analisa todas as colunas fornecidas como entrada e, dependendo do tipo de valores nas colunas, gera um conjunto de estatísticas relevante, conforme descrito na seção [resultados](#results) .

1. Execute o pipeline.

## <a name="results"></a>Resultados

O relatório do módulo pode incluir as estatísticas a seguir. 

|Nome da coluna|Description|
|------|------|  
|**Recurso**|Nome da coluna|
|**Count**|Contagem de todas as linhas|
|**Contagem de valor exclusivo**|Número de valores exclusivos na coluna|
|**Contagem de valor ausente**|Número de valores exclusivos na coluna|
|**Min**|Valor mais baixo na coluna|  
|**Max**|Valor mais alto na coluna|
|**Mean**|Média de todos os valores de coluna|
|**Desvio médio**|Desvio médio de valores de coluna|
|**Primeiro quartil**|Valor no primeiro quartil|
|**Median**|Valor da coluna mediana|
|**terceiro quartil**|Valor no terceiro quartil|
|**Modo**|Modo de valores de coluna|
|**Range**|Inteiro que representa o número de valores entre os valores máximo e mínimo|
|**Variação de amostra**|Variação para a coluna; consulte a observação|
|**Desvio padrão de exemplo**|Desvio padrão para a coluna; consulte a observação|
|**Distorção de exemplo**|Distorção para a coluna; consulte a observação|
|**Curtose de amostra**|Curtose da coluna; consulte a observação|
|**P 0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="technical-notes"></a>Observações técnicas

- Para colunas não numéricas, somente os valores para contagem, contagem de valor exclusivo e contagem de valor ausente são computados. Para outras estatísticas, um valor nulo é retornado.

- As colunas que contêm valores Boolianos são processadas usando estas regras:

    - Ao calcular min, um AND lógico é aplicado.
    
    - Ao calcular Max, um OR lógico é aplicado
    
    - Ao calcular o intervalo, o módulo verifica primeiro se o número de valores exclusivos na coluna é igual a 2.
    
    - Ao computar qualquer estatística que exija cálculos de ponto flutuante, os valores de true são tratados como 1,0 e os valores de false são tratados como 0,0.

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.  
