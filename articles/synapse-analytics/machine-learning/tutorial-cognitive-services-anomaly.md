---
title: 'Tutorial: Detecção de anomalias com os Serviços Cognitivos'
description: Tutorial sobre como aproveitar os Serviços Cognitivos para detecção de anomalias no Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463380"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Tutorial: Detecção de anomalias com os Serviços Cognitivos (versão prévia)

Neste tutorial, você aprenderá a enriquecer facilmente seus dados no Azure Synapse com os [Serviços Cognitivos](https://go.microsoft.com/fwlink/?linkid=2147492). Usaremos o [Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2147493) para executar a detecção de anomalias. Um usuário no Azure Synapse pode simplesmente selecionar uma tabela para enriquecer a detecção de anomalias.

Este tutorial abrange:

> [!div class="checklist"]
> - Etapas para obter um conjunto de dados de tabela do Spark que contém dados de série temporal.
> - Use uma experiência de assistente no Azure Synapse para enriquecer dados usando o Serviço Cognitivo do Detector de Anomalias.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Para usar este tutorial, conclua as etapas de pré-configuração descritas nele. [Configurar os Serviços Cognitivos no Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Criar uma tabela do Spark

Você precisará de uma tabela do Spark para este tutorial.

1. Baixe o seguinte arquivo de notebook que contém um código para gerar uma tabela do Spark: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Carregue o arquivo para seu workspace do Azure Synapse.
![Carregar notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Abra o arquivo do notebook e escolha **Executar Todas** as células.
![Criar tabela do Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Uma tabela do Spark com o nome **anomaly_detector_testing_data** agora deve aparecer no banco de dados do Spark padrão.

## <a name="launch-cognitive-services-wizard"></a>Iniciar assistente dos Serviços Cognitivos

1. Clique com o botão direito do mouse na tabela do Spark criada na etapa anterior. Selecione "Machine Learning -> Enriquecer com o modelo existente" para abrir o assistente.
![Iniciar assistente de pontuação](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Um painel de configuração será exibido e você deverá selecionar um modelo dos Serviços Cognitivos. Selecione Detector de Anomalias.

![Iniciar assistente de pontuação – etapa 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para fazer a autenticação nos Serviços Cognitivos, você precisa fazer referência ao segredo usado em seu Key Vault. As entradas abaixo estão dependendo das [etapas de pré-requisito](tutorial-configure-cognitive-services-synapse.md) que você deve ter concluído antes desta etapa.

- **Assinatura do Azure**: selecione a assinatura do Azure à qual seu cofre de chaves pertence.
- **Conta dos Serviços Cognitivos**: esse é o recurso de Análise de Texto ao qual você vai se conectar.
- **Serviço Vinculado do Azure Key Vault**: como parte das etapas de pré-requisitos, você criou um serviço vinculado para o recurso Análise de Texto. Selecione-o aqui.
- **Nome do segredo**: esse é o nome do segredo no cofre de chaves que contém a chave para fazer a autenticação no recurso dos Serviços Cognitivos.

![Fornecer detalhes do Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Configurar a Detecção de Anomalias

Em seguida, você precisa configurar a detecção de anomalias. Forneça os seguintes detalhes:

- Granularidade: a taxa na qual os dados são amostrados. Por exemplo, se os seus dados tiverem um valor para cada minuto, sua granularidade será por minuto. Escolha **mensalmente** 

- Carimbo de data/hora: coluna que representa a hora da série. Escolha a coluna **carimbo de data/hora**

- Valor de série temporal: coluna que representa o valor da série na hora especificada pela coluna Carimbo de data/hora. Escolha a coluna **valor**

- Agrupamento: coluna que agrupa a série. Ou seja, todas as linhas que têm o mesmo valor nesta coluna devem formar uma série temporal. Escolha a coluna **grupo**

Quando terminar, selecione **Abrir Notebook**. Isso vai gerar um notebook para você com o código do PySpark que executa a detecção de anomalias usando os Serviços Cognitivos do Azure.

![Configurar detector de anomalias](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Abrir notebook e executar

O notebook que você acabou de abrir está usando a [Biblioteca MMLSpark](https://github.com/Azure/mmlspark) para se conectar aos Serviços Cognitivos.

Os detalhes do Azure Key Vault fornecidos permitem que você referencie com segurança seus segredos dessa experiência sem revelá-los.

Agora você pode **Executar Todas** as células para executar a detecção de anomalias. Saiba mais sobre os [Serviços Cognitivos – Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2147493).

![Executar a Detecção de Anomalias](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Análise de sentimento com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)