---
title: Resumir dados
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Resumir dados no Azure Machine Learning para gerar um relatório básico de estatísticas descritivas para as colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477435"
---
# <a name="summarize-data"></a>Resumir dados

Este artigo descreve um módulo do azure Machine Learning designer (visualização).

Use o módulo Resumir dados para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas de resumo são úteis quando você deseja entender as características do conjunto de dados completo. Por exemplo, talvez seja necessário saber:

- Quantos valores ausentes existem em cada coluna?
- Quantos valores únicos existem em uma coluna de recursos?
- Qual é o desvio médio e padrão para cada coluna?

O módulo calcula os escores importantes para cada coluna e retorna uma linha de estatísticas de resumo para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar resumir dados  

1. Adicione o módulo **Resumir dados** ao seu pipeline. Você pode encontrar este módulo na categoria **Funções Estatísticas** no designer.

1. Conecte o conjunto de dados para o qual deseja gerar um relatório.

    Se você quiser reportar apenas algumas colunas, use o módulo [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md) para projetar um subconjunto de colunas para trabalhar.

1. Não são necessários parâmetros adicionais. Por padrão, o módulo analisa todas as colunas fornecidas como entrada e, dependendo do tipo de valores nas colunas, produz um conjunto relevante de estatísticas conforme descrito na seção [Resultados.](#results)

1. Envie o oleoduto.

## <a name="results"></a>Resultados

O relatório do módulo pode incluir as seguintes estatísticas. 

|Nome da coluna|Descrição|
|------|------|  
|**Recurso**|Nome da coluna|
|**Contar**|Contagem de todas as linhas|
|**Contagem de valor única**|Número de valores únicos na coluna|
|**Contagem de valor perdido**|Número de valores únicos na coluna|
|**Min**|Menor valor na coluna|  
|**Max**|Maior valor na coluna|
|**Média**|Média de todos os valores da coluna|
|**Desvio Médio**|Desvio médio dos valores da coluna|
|**Primeiro quartil**|Valor no primeiro quartil|
|**Median**|Valor médio da coluna|
|**Terceiro quartil**|Valor no terceiro quartil|
|**Modo**|Modo de valores de coluna|
|**Gama**|Inteiro representando o número de valores entre os valores máximo e mínimo|
|**Variância amostral**|Variância para coluna; ver Nota|
|**Desvio padrão da amostra**|Desvio padrão para coluna; ver Nota|
|**Inclinação da amostra**|Inclinação para coluna; ver Nota|
|**Amostra de Kurtose**|Kurtose para coluna; ver Nota|
|**P0.5**|Percentual de 0,5%|
|**P1**|Percentil 1%|
|**P5**|Percentil 5%|
|**P95**|Percentil 95%|
|**P99.5**|Percentil 99,5% |

## <a name="technical-notes"></a>Observações técnicas

- Para colunas não numéricas, apenas os valores para Contagem, Contagem de valor exclusivo e Contagem de valor ausente são calculados. As outras estatísticas retornarão um valor nulo.

- As colunas que contêm valores booleanos são processadas usando estas regras:

    - Ao calcular Mín, um AND lógico é aplicado.
    
    - Ao calcular Max, uma OR lógica é aplicada
    
    - Ao calcular Intervalo, o módulo primeiro verifica se o número de valores exclusivos na coluna é igual a 2.
    
    - Ao calcular qualquer estatística que requeira cálculos de ponto flutuante, os valores True são tratados como 1,0 e os valores False são tratados como 0,0.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.  
