---
title: 'Tutorial: Assistente de pontuação do modelo de machine learning para pools de SQL dedicados'
description: Tutorial sobre como usar o assistente de pontuação do modelo de machine learning para enriquecer dados em pools de SQL dedicados.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f5c5edc067b3f7b525fd129462c48ca50fdafc8f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314021"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-dedicated-sql-pools"></a>Tutorial: Assistente de pontuação do modelo de machine learning para pools de SQL dedicados

Saiba como enriquecer facilmente seus dados em pools de SQL dedicados com modelos de machine learning preditivos.  Os modelos que os cientistas de dados criam agora podem ser facilmente acessados por profissionais de dados para análise preditiva. Um profissional de dados no Azure Synapse pode simplesmente selecionar um modelo do registro de modelos do Azure Machine Learning para implantação em pools de SQL do Synapse e iniciar previsões para enriquecer os dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Treinar um modelo de machine Learning preditivo e registrar o modelo no registro de modelos do Azure Machine Learning
> - Usar o assistente de pontuação do SQL para iniciar previsões no pool de SQL dedicado

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.
- Pool de SQL dedicado no seu workspace do Synapse Analytics. Para obter detalhes, confira [Criar um pool de SQL dedicado](../quickstart-create-sql-pool-studio.md).
- Serviço vinculado do Azure Machine Learning em seu workspace do Synapse Analytics. Para obter mais detalhes, confira [Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Treinar um modelo no Azure Machine Learning

Antes de começar, verifique se sua versão do **sklearn** é a 0.20.3.

Antes de executar todas as células no notebook, verifique se a instância de computação está em execução.

![Verificar computação AML](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Navegue até o Workspace do Azure Machine Learning.

1. Baixe [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Inicie o Workspace do Azure Machine Learning no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Vá para **Notebooks** e clique em **Carregar arquivos**, selecione o "Predict NYC Taxi Tips.ipynb" que você baixou e carregue o arquivo.
   ![Carregar arquivo](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Depois que o notebook for carregado e aberto, clique em **Executar todas as células**.

   Uma das células pode falhar e solicitar que você se autentique no Azure. Fique atento a isso nas saídas de célula e faça a autenticação no navegador seguindo o link e inserindo o código. Em seguida, execute o Notebook.

1. O notebook treinará um modelo ONNX e o registrará com o MLFlow. Acesse **Modelos** para verificar se o novo modelo está registrado corretamente.
   ![Modelo no registro](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A execução do notebook também exportará os dados de teste para um arquivo CSV. Baixe o arquivo CSV em seu sistema local. Posteriormente, você importará o arquivo CSV no pool de SQL dedicado e usará os dados para testar o modelo.

   O arquivo CSV é criado na mesma pasta que o arquivo do notebook. Clique em "Atualizar" no Explorador de Arquivos se você não o vir imediatamente.

   ![Arquivo CSV](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Iniciar previsões com o assistente de pontuação do SQL

1. Abra o workspace do Azure Synapse com o Synapse Studio.

1. Navegue até **Dados** -> **Vinculado** -> **Contas de Armazenamento**. Carregue `test_data.csv` na conta de armazenamento padrão.

   ![Carregar dados](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Acesse **Desenvolver** -> **Scripts SQL**. Crie um script de SQL para carregar `test_data.csv` em seu pool de SQL dedicado.

   > [!NOTE]
   > Atualize a URL do arquivo neste script antes de executá-lo.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Carregar dados no pool do SQL dedicado](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Acesse **Dados** -> **Workspace**. Abra o assistente de pontuação do SQL clicando com o botão direito do mouse na tabela do pool de SQL dedicado. Selecione **Machine Learning** -> **Enriquecer com o modelo existente**.

   > [!NOTE]
   > A opção de machine Learning não é exibida a menos que você tenha um serviço vinculado criado para o Azure Machine Learning (confira **Pré-requisitos** no início deste tutorial).

   ![Opção de Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecione um Workspace do Azure Machine Learning vinculado na caixa suspensa. Isso carrega uma lista de modelos de machine learning do registro de modelos do Workspace do Azure Machine Learning escolhido. Atualmente, há suporte apenas para modelos ONNX, portanto, isso exibirá apenas modelos ONNX.

1. Selecione o modelo que você acabou de treinar e clique em **Continuar**.

   ![Selecionar modelo do Azure Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Em seguida, mapeie as colunas da tabela para as entradas do modelo e especifique as saídas do modelo. Se o modelo for salvo no formato MLFlow e a assinatura do modelo for populada, o mapeamento será feito automaticamente para você usando uma lógica com base na similaridade de nomes. A interface também dá suporte ao mapeamento manual.

   Clique em **Continuar**.

   ![Tabela para mapeamento de modelo](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. O código T-SQL gerado é encapsulado dentro de um procedimento armazenado. É por isso que você precisa fornecer um nome ao procedimento armazenado. O binário do modelo, incluindo metadados (versão, descrição etc.), será copiado fisicamente do Azure Machine Learning para uma tabela de pool de SQL dedicado. Portanto, você precisa especificar em qual tabela salvar o modelo. Você pode escolher "Usar uma tabela existente" ou "Criar uma tabela". Depois de terminar, clique em **Implantar modelo + abrir editor** para implantar o modelo e gerar um script de previsão do T-SQL.

   ![Criar procedimento](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Depois que o script for gerado, clique em "Executar" para executar a pontuação e obter previsões.

   ![Executar previsões](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funcionalidades de Machine Learning no Azure Synapse Analytics (versão prévia dos workspaces)](what-is-machine-learning.md)
