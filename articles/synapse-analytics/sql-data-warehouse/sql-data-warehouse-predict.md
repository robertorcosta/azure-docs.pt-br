---
title: Pontuar modelos de aprendizado de máquina com previsão
description: Saiba como pontuar modelos de aprendizado de máquina usando a função de previsão T-SQL no Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ef56274e0bda3f1a9d494852520a77ecdfc25799
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047999"
---
# <a name="score-machine-learning-models-with-predict"></a>Pontuar modelos de aprendizado de máquina com previsão

Synapse SQL fornece a capacidade de pontuar modelos de aprendizado de máquina usando a linguagem T-SQL familiar. Com a [previsão](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)do T-SQL, você pode trazer seus modelos de aprendizado de máquina existentes treinados com dados históricos e pontua-los dentro dos limites de segurança do seu data warehouse. A função PREDICT usa um modelo de [ONNX (Open Neural Network Exchange)](https://onnx.ai/) e os dados como entradas. Esse recurso elimina a etapa de mover dados valiosos para fora do data warehouse para pontuação. Ele visa capacitar os profissionais de dados a implantar facilmente modelos de aprendizado de máquina com a interface T-SQL familiar, bem como colaborar perfeitamente com cientistas de dados trabalhando com a estrutura certa para sua tarefa.

> [!NOTE]
> Atualmente, não há suporte para essa funcionalidade no SQL sob demanda.

A funcionalidade requer que o modelo seja treinado fora do Synapse SQL. Depois de criar o modelo, carregue-o no data warehouse e pontua-lo com a sintaxe de previsão T-SQL para obter informações dos dados.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Treinando o modelo

Synapse SQL espera um modelo pré-treinado. Tenha em mente os seguintes fatores ao treinar um modelo de aprendizado de máquina que é usado para executar previsões no Synapse SQL.

- Synapse SQL só dá suporte a modelos de formato ONNX. ONNX é um formato de modelo de software livre que permite que você troque modelos entre várias estruturas para habilitar a interoperabilidade. Você pode converter seus modelos existentes no formato ONNX usando estruturas que dão suporte a ele nativamente ou que convertidam pacotes disponíveis. Por exemplo, o pacote [sklearn-onnx](https://github.com/onnx/sklearn-onnx) converte os modelos scikit-Learn para onnx. O [repositório GitHub ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format) fornece uma lista de estruturas e exemplos com suporte.

   Se você estiver usando o [ml automatizado](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) para treinamento, certifique-se de definir o parâmetro *enable_onnx_compatible_models* como true para produzir um modelo de formato onnx. O [Notebook Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) mostra um exemplo de como usar o AutoML para criar um modelo de aprendizado de máquina do formato ONNX.

- Os seguintes tipos de dados têm suporte para os dados de entrada:
    - int, bigint, real, float
    - Char, varchar, nvarchar

- Os dados de Pontuação precisam estar no mesmo formato que os dados de treinamento. Tipos de dados complexos, como matrizes multidimensionais, não são suportados por previsão. Portanto, para treinamento, certifique-se de que cada entrada do modelo corresponda a uma única coluna da tabela de pontuação, em vez de passar uma única matriz contendo todas as entradas.

- Certifique-se de que os nomes e tipos de dados das entradas do modelo correspondam aos nomes de coluna e tipos de dados dos novos dados de previsão. A visualização de um modelo ONNX usando várias ferramentas de software livre disponíveis online pode ajudar ainda mais na depuração.

## <a name="loading-the-model"></a>Carregando o modelo

O modelo é armazenado em uma tabela de usuário do SQL do Synapse como uma cadeia de caracteres hexadecimal. Colunas adicionais, como ID e descrição, podem ser adicionadas à tabela modelo para identificar o modelo. Use varbinary (max) como o tipo de dados da coluna de modelo. Este é um exemplo de código para uma tabela que pode ser usada para armazenar modelos:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Depois que o modelo for convertido em uma cadeia de caracteres hexadecimal e a definição de tabela especificada, use o [comando de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ou o polybase para carregar o modelo na tabela SQL Synapse. O exemplo de código a seguir usa o comando de cópia para carregar o modelo.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Pontuação do modelo

Depois que o modelo e os dados são carregados no data warehouse, use a função de **previsão T-SQL** para pontuar o modelo. Verifique se os novos dados de entrada estão no mesmo formato que os dados de treinamento usados para criar o modelo. A previsão de T-SQL usa duas entradas: modelo e novos dados de entrada de Pontuação e gera novas colunas para a saída. O modelo pode ser especificado como uma variável, um literal ou um sub_query escalar. Use [WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15) para especificar um conjunto de resultados nomeado para o parâmetro data.

O exemplo a seguir mostra uma consulta de exemplo usando a função de previsão. Uma coluna adicional com a *Pontuação* de nome e o tipo de dados *float* é criada contendo os resultados da previsão. Todas as colunas de dados de entrada e as colunas de previsão de saída estão disponíveis para exibição com a instrução SELECT. Para obter mais detalhes, consulte [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a função PREDICT, consulte [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).
