---
title: Analisar dados com o Azure Machine Learning
description: Use Azure Machine Learning para criar um modelo de aprendizado de máquina preditiva com base nos dados armazenados no Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416180"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial usa Azure Machine Learning para criar um modelo de aprendizado de máquina preditiva com base nos dados armazenados no Azure Synapse. Especificamente, isso compila uma campanha de marketing direcionado da Adventure Works, uma loja de bicicletas, prevendo se um cliente tem probabilidade de comprar uma bicicleta ou não.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para realizar este tutorial, você precisa:

* Um pool do SQL pré-carregado com dados de exemplo de AdventureWorksDW. Para provisionar isso, consulte [criar um pool de SQL](create-data-warehouse-portal.md) e optar por carregar os dados de exemplo. Se você já tiver um data warehouse, mas não tiver dados de exemplo, poderá [carregar dados de exemplo manualmente](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. obter os dados
Os dados estão na exibição dbo.vTargetMail no banco de dados AdventureWorksDW. Para ler esses dados:

1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/) e clique em Meus Testes.
2. Clique em **+ novo** na parte inferior esquerda da tela e selecione **experimento em branco**.
3. Insira um nome para o seu teste: Marketing Direcionado.
4. Arraste o módulo **importar dados** em **entrada e saída de dados** do painel módulos para a tela.
5. Especifique os detalhes do seu pool SQL no painel Propriedades.
6. Especifique a **consulta** do banco de dados para ler os dados de interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Execute o experimento clicando em **executar** na tela do experimento.

![Execute o experimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Após o teste terminar de ser executado com êxito, clique na porta de saída na parte inferior do módulo Leitor e selecione **Visualizar** para ver os dados importados.

![Exibir dados importados](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. limpar os dados
Para limpar os dados, remova algumas colunas que não são relevantes para o modelo. Para fazer isso:

1. Arraste o módulo **selecionar colunas no conjunto** de **dados em data transformation < manipulação** na tela. Conecte este módulo ao módulo **importar dados** .
2. Clique em **Iniciar seletor de colunas** no painel Propriedades para especificar quais colunas você deseja remover.

   ![Colunas do projeto](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.

   ![Remover colunas desnecessárias](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. compilar o modelo
Dividiremos os dados 80-20: 80% para treinar um modelo de aprendizado de máquina e 20% para testar o modelo. Usaremos os algoritmos de "duas classes" para esse problema de classificação binária.

1. Arraste o módulo **Divisão** na tela.
2. No painel Propriedades, insira 0,8 para a fração de linhas no primeiro conjunto de registros de saída.

   ![Dividir os dados em conjuntos de treinamento e teste.](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Arraste o módulo **Árvore de Decisão Aumentada de duas classes** na tela.
4. Arraste o módulo **treinar modelo** para a tela e especifique as entradas conectando-o à **árvore de decisão aumentada de duas classes** (algoritmo ml) e **dividir** (dados para treinar o algoritmo). 

     ![Conectar o módulo Treinar Modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Depois, clique em **Iniciar seletor de coluna** no painel de Propriedades. Selecione a coluna **BikeBuyer** como a coluna a ser prevista.

   ![Selecionar a Coluna a prever](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. pontuar o modelo
Agora, testaremos o desempenho do modelo nos dados de teste. Vamos comparar o algoritmo de nossa escolha com um algoritmo diferente para ver que tem um desempenho melhor.

1. Arraste o módulo **modelo de Pontuação** para a tela e conecte-o para **treinar o modelo** e **dividir os** módulos de dados.

   ![Pontuar o modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Arraste o **Computador de Ponto de Bayes de Duas Classes** na tela de teste. Vamos comparar o desempenho desse algoritmo em comparação com a Árvore de Decisão Aumentada de Duas Classes.
3. Copie e cole os módulos Modelo de Treinamento e Modelo de Pontuação na tela.
4. Arraste o módulo **Avaliar Modelo** na tela para comparar os dois algoritmos.
5. **Execute** o teste.

   ![Execute o experimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Clique na porta de saída na parte inferior do módulo Avaliar Modelo e clique em Visualizar.

   ![Visualizar os resultados de avaliação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

As métricas fornecidas são a curva ROC, o diagrama de recall de precisão e a curva de elevação. Ao examinar essas métricas, podemos ver que o primeiro modelo teve um desempenho melhor do que o segundo. Para examinar o que o primeiro modelo prevê, clique na porta de saída do modelo de Pontuação e clique em Visualizar.

![Visualizar os resultados da pontuação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Você verá duas ou mais colunas adicionadas ao seu conjunto de dados de teste.

* Probabilidades Pontuadas: a probabilidade de que um cliente é um comprador de bicicleta.
* Rótulos Pontuados: a classificação feita pelo modelo – comprador de bicicleta (1) ou não (0). Esse limite de probabilidade para a rotulagem é definido como 50% e pode ser ajustado.

Ao comparar a coluna BikeBuyer (real) com os Rótulos Pontuados (previsão), é possível ver o desempenho do modelo. Em seguida, você pode usar esse modelo para fazer previsões para novos clientes e publicar esse modelo como um serviço Web ou gravar resultados de volta para o Azure Synapse.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a criação de modelos de aprendizado de máquina de previsão, consulte [Introdução ao Machine Learning no Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).