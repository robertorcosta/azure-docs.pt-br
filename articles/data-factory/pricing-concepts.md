---
title: Entendendo os preços da Fábrica de Dados do Azure através de exemplos
description: Este artigo explica e demonstra o modelo de preços do Azure Data Factory com exemplos detalhados
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414936"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Noções básicas de preços do Data Factory por meio de exemplos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explica e demonstra o modelo de preços do Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não têm a intenção de implicar preços reais.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados do AWS S3 para o Armazenamento de Blobs do Azure por hora

Neste cenário, é ideal copiar dados do AWS S3 para o Armazenamento de Blobs do Azure em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3.

2. Um conjunto de dados de saída para os dados no Armazenamento do Azure.

3. Um gatilho de agenda para executar o pipeline a cada hora.

   ![Cenário1](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 Execuções de atividade (1 para execução do gatilho, 1 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 2 registros de execução de monitoramento repetidos (1 para execução de pipeline, 1 para execução de atividade) |

**Total de preços do cenário: US$ 0,16811**

- Operações do Data Factory = **US$ 0,0001**
  - Leitura/gravação = 10\*00001 = US$ 0,0001 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 2\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,168**
  - Execuções de atividade = 001\*2 = 0,002 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiar dados e transformar com o Azure Databricks por hora

Neste cenário, você deseja copiar dados do AWS S3 para o Armazenamento de Blobs do Azure e transformar os dados com o Azure Databricks em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3 e um conjunto de dados de saída para os dados no Armazenamento do Azure.
2. Uma atividade do Azure Databricks para a transformação de dados.
3. Um gatilho de agenda para executar o pipeline a cada hora.

![Cenário2](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 3 Execuções de atividade (1 para execução do gatilho, 2 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 3 registros de execução de monitoramento repetidos (1 para execução de pipeline, 2 para execução de atividade) |
| Suposição da atividade Executar Databricks: tempo de execução = 10 min | 10 minutos de execução de atividade do pipeline externo |

**Total de preços do cenário: US$ 0,16916**

- Operações do Data Factory = **US$ 0,00012**
  - Leitura/gravação = 11\*00001 = US$ 0,00011 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 3\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,16904**
  - Execuções de atividade = 001\*3 = 0,003 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)
  - Atividade de pipeline externa = US$ 0,000041 (proporcional a 10 minutos de tempo de execução. US$ 0,00025/hora no Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copiar dados e transformar com parâmetros dinâmicos por hora

Nesse cenário, você deseja copiar dados do AWS S3 para o Armazenamento de Blobs do Azure e transformá-los com o Azure Databricks (com parâmetros dinâmicos no script) em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3, um conjunto de dados de saída para os dados no Armazenamento do Azure.
2. Uma atividade de pesquisa para passar parâmetros dinamicamente para o script de transformação.
3. Uma atividade do Azure Databricks para a transformação de dados.
4. Um gatilho de agenda para executar o pipeline a cada hora.

![Cenário3](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 4 Execuções de atividade (1 para execução do gatilho, 3 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 4 registros de execução de monitoramento repetidos (1 para execução de pipeline, 3 para execução de atividade) |
| Suposição da atividade de pesquisa: tempo de execução = 1 min | 1 minuto de execução da atividade do pipeline |
| Suposição da atividade Executar Databricks: tempo de execução = 10 min | 10 minutos de execução de atividade do pipeline externo |

**Total de preços do cenário: US$ 0,17020**

- Operações do Data Factory = **US$ 0,00013**
  - Leitura/gravação = 11\*00001 = US$ 0,00011 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 4\*000005 = US$ 0,00002 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,17007**
  - Execuções de atividade = 001\*4 = 0,004 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)
  - Atividade de pipeline = US$ 0,00003 (proporcional a 1 minuto de tempo de execução. US$ 0,002/hora no Azure Integration Runtime)
  - Atividade de pipeline externa = US$ 0,000041 (proporcional a 10 minutos de tempo de execução. US$ 0,00025/hora no Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando depuração de fluxo de dados de mapeamento para um dia de trabalho normal

Como engenheiro de dados, você é responsável por projetar, construir e testar fluxos de dados de mapeamento todos os dias. Você entra na UI do ADF pela manhã e habilita o modo Debug para Fluxos de Dados. O TTL padrão para sessões de depuração é de 60 minutos. Você trabalha durante todo o dia por 8 horas, então sua sessão de Depuração nunca expira. Portanto, sua cobrança pelo dia será:

**8 (horas) x 8 (núcleos otimizados para computação) x $0,193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transforme dados em blob store com mapeamento de fluxos de dados

Neste cenário, você deseja transformar dados no Blob Store visualmente em fluxos de dados de mapeamento adf em um horário de hora em hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de Fluxo de Dados com a lógica de transformação.

2. Um conjunto de dados de entrada para os dados no Azure Storage.

3. Um conjunto de dados de saída para os dados no Armazenamento do Azure.

4. Um gatilho de agenda para executar o pipeline a cada hora.

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 Execuções de atividade (1 para execução do gatilho, 1 para execução da atividade) |
| Suposições de fluxo de dados: tempo de execução = 10 min + 10 min TTL | 10 \* 16 núcleos de Computação Geral com TTL de 10 |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 2 registros de execução de monitoramento repetidos (1 para execução de pipeline, 1 para execução de atividade) |

**Preço total do cenário: $1.4631**

- Operações do Data Factory = **US$ 0,0001**
  - Leitura/gravação = 10\*00001 = US$ 0,0001 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 2\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução &amp; de Orquestração de Gasodutos = **$1.463**
  - Execuções de atividade = 001\*2 = 0,002 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de fluxo de dados = $1.461 rateados por 20 minutos (tempo de execução de 10 minutos + 10 minutos de TTL). $0.274/hora no Azure Integration Runtime com 16 núcleos de computação geral

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu o preço do Azure Data Factory, pode começar a usá-lo!

- [Crie uma fábrica de dados usando a ui da Fábrica de Dados do Azure](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Autoria visual na Fábrica de Dados Azure](author-visually.md)
