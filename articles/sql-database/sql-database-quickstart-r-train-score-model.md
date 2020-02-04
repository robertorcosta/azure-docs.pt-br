---
title: Criar e treinar um modelo preditivo no R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Criar um modelo preditivo simples em R usando os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia) e, em seguida, prever um resultado usando dados novos.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768458"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Início Rápido: Criar e treinar um modelo preditivo em R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Neste início rápido, você criará e treinará um modelo preditivo usando o R, salvará o modelo em uma tabela no banco de dados e usará o modelo para prever valores de novos dados usando os Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md) com uma [regra de firewall no nível do servidor](sql-database-server-level-firewall-rule.md)
- [Serviços do Machine Learning](sql-database-machine-learning-services-overview.md) com R habilitado. [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).
- SSMS ([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms))

> [!NOTE]
> Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente.

Este exemplo usa um modelo de regressão simples para prever a distância de parada de um carro com base na velocidade usando o conjunto de dados de **carros** incluído com o R.

> [!TIP]
> Muitos conjuntos de dados estão incluídos com o runtime do R, para obter uma lista dos conjuntos de valores instalados, digite `library(help="datasets")` no prompt de comando do R.

## <a name="create-and-train-a-predictive-model"></a>Criar e treinar um modelo preditivo

Os dados de velocidade do carro no conjunto de dados **carros** contém duas colunas, ambas numéricas: **dist** e **velocidade**. Os dados incluem várias observações de parada em velocidades diferentes. Com esses dados, você criará um modelo de regressão linear que descreve a relação entre a velocidade do carro e a distância necessária para que ele pare.

Os requisitos de um modelo linear são simples:
- Defina uma fórmula que descreve a relação entre a variável dependente *velocidade* e a variável independente *distância*.
- Forneça dados de entrada para usar no treinamento do modelo.

> [!TIP]
> Se você precisar de uma atualização sobre modelos lineares, experimente este tutorial, que descreve o processo de ajuste de um modelo usando o rxLinMod: [Ajustando modelos lineares](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Nas etapas a seguir, você configurará os dados de treinamento, criará um modelo de regressão, treinará esse modelo usando os dados de treinamento e o salvará em uma tabela SQL.

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Crie a tabela **CarSpeed** para salvar os dados de treinamento.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Crie um modelo de regressão usando `rxLinMod`. 

   Para criar o modelo, defina a fórmula dentro do código R e, em seguida, passe **CarSpeed** dos dados de treinamento como um parâmetro de entrada.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     O primeiro argumento para rxLinMod é o parâmetro *formula*, que define a distância como dependente da velocidade. Os dados de entrada são armazenados na variável `CarsData`, que é preenchida pela consulta SQL.

1. Crie uma tabela na qual armazenar o modelo, para que você possa usá-lo posteriormente para previsão. 

   A saída de um pacote de R que cria um modelo é geralmente um **objeto binário**, então a tabela precisa ter uma coluna de tipo **VARBINARY(max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Agora, chame o procedimento armazenado, gere o modelo e salve-o em uma tabela.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Observe que, se você executar esse código uma segunda vez, receberá este erro:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Uma opção para evitar esse erro é atualizar o nome de cada modelo novo. Por exemplo, você pode alterar o nome para algo mais descritivo e incluir o tipo de modelo, o dia em que ele foi criado e assim por diante.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Exibir a tabela de coeficientes

Em geral, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) é limitada a uma única estrutura de dados. No entanto, é possível retornar saídas de outros tipos, como escalares, além da estrutura de dados.

Por exemplo, suponha que você queria treinar um modelo, mas queria exibir imediatamente uma tabela de coeficientes do modelo. Para fazer isso, você cria a tabela de coeficientes como o conjunto de resultados principal e gera o modelo treinado como saída em uma variável SQL. Você pode reutilizar o modelo imediatamente chamando a variável ou pode salvar o modelo em uma tabela, conforme mostrado aqui.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Resultados**

![Modelo treinado com saída adicional](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Pontuar novos dados usando o modelo treinado

*Pontuação* é um termo usado na ciência de dados para significar a geração de previsões, probabilidades ou outros valores com base em novos dados inseridos em um modelo treinado. Você usará o modelo criado na seção anterior para pontuar previsões comparando-as a novos dados.

Você notou que os dados de treinamento originais param a uma velocidade de 40 km/h? Isso ocorre porque os dados originais têm base em um experimento de 1920! Talvez você se pergunte: "quanto tempo um automóvel de 1920 demoraria para parar se ele pudesse chegar a 96 km/h ou até mesmo a 160 km/h?" Para responder a essa pergunta, você pode fornecer alguns novos valores de velocidade para seu modelo.

1. Crie uma tabela com novos dados de velocidade.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Preveja a distância de parada segundo esses novos valores de velocidade.

   Considerando que seu modelo é baseado no algoritmo **rxLinMod** fornecido como parte do pacote **RevoScaleR**, chame a função [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), em vez da função R genérica `predict`.

   Este script de exemplo:
   - Usa uma instrução SELECT para obter um único modelo da tabela
   - Passa-a como um parâmetro de entrada
   - Chama a função `unserialize` no modelo
   - Aplica a função `rxPredict` com os argumentos apropriados ao modelo
   - Fornece os novos dados de entrada

   > [!TIP]
   > Para a pontuação em tempo real, consulte as [funções de serialização](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fornecidas pelo RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Resultados**

   ![Conjunto de resultados para previsão da distância de parada](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Neste script de exemplo, a função `str` é adicionada durante a fase de teste para verificar o esquema dos dados retornado de R. Você pode remover a instrução mais tarde.
>
> Os nomes de coluna usados no script R não são necessariamente passados para a saída do procedimento armazenado. Aqui, a cláusula WITH RESULTS define novos nomes de coluna.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia), confira os seguintes artigos.

- [Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-create-script.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar usando dados do R e do SQL nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)
