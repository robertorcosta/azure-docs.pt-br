---
title: 'Tutorial: Análise de sentimento com os Serviços Cognitivos'
description: Tutorial sobre como aproveitar os Serviços Cognitivos para análise de sentimento no Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463612"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Tutorial: Análise de sentimento com os Serviços Cognitivos (versão prévia)

Neste tutorial, você aprenderá a enriquecer facilmente seus dados no Azure Synapse com os [Serviços Cognitivos](https://go.microsoft.com/fwlink/?linkid=2147492). Usaremos os recursos de [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) para executar a análise de sentimento. Um usuário no Azure Synapse pode simplesmente selecionar uma tabela que contém uma coluna de texto para enriquecimento com sentimentos. Esses sentimentos podem ser positivos, negativos, mistos ou neutros, e uma probabilidade também será retornada.

Este tutorial abrange:

> [!div class="checklist"]
> - As etapas para obter um conjunto de dados de uma tabela do Spark que contém uma coluna de texto para análise de sentimento.
> - O uso de uma experiência de assistente no Azure Synapse para enriquecer dados usando o Serviço Cognitivo de Análise de Texto.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Para usar este tutorial, conclua as etapas de pré-configuração descritas nele. [Configurar os Serviços Cognitivos no Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Criar uma tabela do Spark

Você precisará de uma tabela do Spark para este tutorial.

1. Baixe o seguinte arquivo CSV que contém um conjunto de dados para análise de texto: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Carregue o arquivo em sua conta de armazenamento ADLSGen2 do Azure Synapse.
![Carregar dados](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Crie uma tabela do Spark baseada no arquivo .csv clicando com o botão direito do mouse no arquivo e selecionando **Novo Notebook -> Criar tabela do Spark**.
![Criar tabela do Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nomeie a tabela na célula de código e execute o notebook em um Pool do Spark. Lembre-se de definir "header = True".
![Executar Notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Iniciar assistente dos Serviços Cognitivos

1. Clique com o botão direito do mouse na tabela do Spark criada na etapa anterior. Selecione "Machine Learning -> Enriquecer com o modelo existente" para abrir o assistente.
![Iniciar assistente de pontuação](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Um painel de configuração será exibido e você deverá selecionar um modelo dos Serviços Cognitivos. Selecione Análise de texto – Análise de Sentimento.

![Iniciar assistente de pontuação – etapa 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para fazer a autenticação nos Serviços Cognitivos, você precisa fazer referência ao segredo usado em seu Key Vault. As entradas abaixo estão dependendo das [etapas de pré-requisito](tutorial-configure-cognitive-services-synapse.md) que você deve ter concluído antes desta etapa.

- **Assinatura do Azure**: selecione a assinatura do Azure à qual seu cofre de chaves pertence.
- **Conta dos Serviços Cognitivos**: esse é o recurso de Análise de Texto ao qual você vai se conectar.
- **Serviço Vinculado do Azure Key Vault**: como parte das etapas de pré-requisitos, você criou um serviço vinculado para o recurso Análise de Texto. Selecione-o aqui.
- **Nome do segredo**: esse é o nome do segredo no cofre de chaves que contém a chave para fazer a autenticação no recurso dos Serviços Cognitivos.

![Fornecer detalhes do Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configurar a Análise de Sentimento

Em seguida, você precisa configurar a análise de sentimento. Selecione os seguintes detalhes:
- **Idioma**: selecione o idioma do texto no qual deseja executar a análise de sentimentos. Selecione **EN**.
- **Coluna de texto**: é a coluna de texto no conjunto de dados que você deseja analisar para determinar o sentimento. Selecione a coluna da tabela **comentário**.

Quando terminar, clique em **Abrir Notebook**. Isso vai gerar um notebook para você com o código PySpark que executa a análise de sentimento usando os Serviços Cognitivos do Azure.

![Configurar a Análise de Sentimento](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Abrir notebook e executar

O notebook que você acabou de abrir está usando a [Biblioteca MMLSpark](https://github.com/Azure/mmlspark) para se conectar aos Serviços Cognitivos.

Os detalhes do Azure Key Vault fornecidos permitem que você referencie com segurança seus segredos dessa experiência sem revelá-los.

Agora, você pode **Executar todas** as células para enriquecer os dados com sentimentos. Os sentimentos serão retornados como positivo/negativo/neutro/misto e você também obterá as probabilidades por sentimento. Saiba mais sobre os [Serviços Cognitivos – Análise de sentimento](https://go.microsoft.com/fwlink/?linkid=2147792).

![Executar a Análise de Sentimento](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Próximas etapas
- [Tutorial: Detecção de anomalias com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funcionalidades de Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)