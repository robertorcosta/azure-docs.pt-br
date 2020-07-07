---
title: Guia de desempenho e escalabilidade da atividade de cópia
description: Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados em Azure Data Factory quando você usa a atividade de cópia.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414169"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guia de desempenho e escalabilidade da atividade de cópia

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se você deseja executar uma migração de dados em larga escala do data Lake ou Enterprise data warehouse (EDW) para o Azure, ou se deseja ingerir dados em escala de diferentes fontes para o Azure para Big Data Analytics, é essencial alcançar o desempenho e a escalabilidade ideais.  O Azure Data Factory fornece um mecanismo de desempenho, resiliente e econômico para ingerir dados em escala, tornando-o uma ótima opção para os engenheiros de dados que buscam criar pipelines de ingestão de dados altamente funcionais e escalonáveis.

Depois de ler este artigo, você poderá responder as seguintes perguntas:

- Que nível de desempenho e escalabilidade posso conseguir usando a atividade de cópia do ADF para cenários de migração de dados e de ingestão de dados?

- Quais etapas devo tomar para ajustar o desempenho da atividade de cópia do ADF?
- Quais botões de otimização de desempenho do ADF posso utilizar para otimizar o desempenho de uma única execução da atividade de cópia?
- Quais outros fatores fora do ADF a considerar ao otimizar o desempenho da cópia?

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte a [visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade atingíveis usando o ADF

O ADF oferece uma arquitetura sem servidor que permite o paralelismo em diferentes níveis, possibilitando aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como a IOPS de armazenamento e a largura de banda para maximizar a taxa de transferência da movimentação de dados para o seu ambiente.  Isso significa que a taxa de transferência que você pode obter pode ser estimada medindo a taxa de transferência mínima oferecida pelo armazenamento de dados de origem, o armazenamento de dados de destino e a largura de banda de rede entre a origem e o destino.  A tabela a seguir calcula a duração da cópia com base no tamanho dos dados e no limite de largura de banda do seu ambiente. 

| Tamanho dos dados/ <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | mínimo de 2,7    | mínimo de 1,4   | mínimo de 0,3   | mínimo de 0,1  | mínimo de 0, 3 | mínimo de 0, 1 | mínimo de 0,0   |
| **10 GB**                   | mínimo de 27,3   | mínimo de 13,7  | mínimo de 2,7   | mínimo de 1,3  | mínimo de 0,3  | mínimo de 0,1  | mínimo de 0, 3  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0, 5 horas | 0, 2 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0, 5 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0, 2 dias |
| **100 TB**                  | 194,2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dia    | 0,2 dias  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0, 6 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

A cópia do ADF é escalonável em diferentes níveis:

![como a cópia do ADF é dimensionada](media/copy-activity-performance/adf-copy-scalability.png)

- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando o [loop For Each](control-flow-for-each-activity.md).
- Uma só atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar o Azure Integration Runtime, você pode especificar [até 256 DIUs](#data-integration-units) para cada atividade de cópia por meio da computação sem servidor; ao usar o runtime de integração auto-hospedada, você pode escalar verticalmente o computador no modo manual ou escalá-lo horizontalmente para vários computadores ([até quatro nós](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e uma só atividade de cópia particionará o conjunto de arquivos em todos os nós.
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho

Siga estas etapas para ajustar o desempenho do seu serviço de Azure Data Factory com a atividade de cópia.

1. **Pegue um conjunto de testes e estabeleça uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia em um exemplo de dados representativos. O conjunto de dados que você escolher deve representar seus padrões de dado típicos (estrutura de pastas, padrão de arquivo, esquema de dados e assim por diante) e é grande o suficiente para avaliar o desempenho da cópia, por exemplo, demora 10 minutos ou mais para que a atividade de cópia seja concluída. Coletar detalhes de execução e características de desempenho após o [monitoramento da atividade de cópia](copy-activity-monitoring.md).

2. **Como maximizar o desempenho de uma única atividade de cópia**:

   Para começar, recomendamos que você primeiro maximize o desempenho usando uma única atividade de cópia.

   - **Se a atividade de cópia estiver sendo executada em um Azure Integration Runtime:** iniciar com valores padrão para [DIU (unidades de integração de dados)](#data-integration-units) e configurações de [cópia paralelas](#parallel-copy) . 

   - **Se a atividade de cópia estiver sendo executada em um Integration Runtime auto-hospedado:** recomendamos que você use um computador dedicado separado do servidor que hospeda o repositório de dados para hospedar o Integration Runtime. Comece com valores padrão para a configuração de [cópia paralela](#parallel-copy) e use um único nó para o ir auto-hospedado.  

   Realize uma execução de teste de desempenho e anote o desempenho obtido, bem como os valores reais usados como DIUs e cópias paralelas. Consulte [monitoramento de atividade de cópia](copy-activity-monitoring.md) sobre como coletar resultados de execução e configurações de desempenho usadas e saiba como [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md) para identificar e resolver o afunilamento. 

   Iterar para realizar execuções de teste de desempenho adicionais seguindo as diretrizes de solução de problemas e ajuste. Quando a execução da atividade de cópia única não puder obter uma melhor taxa de transferência, considere maximizar a taxa de transferência agregada executando várias cópias ao fazer referência simultaneamente à etapa 3.


3. **Como maximizar a taxa de transferência agregada executando várias cópias simultaneamente:**

   Agora que você maximizou o desempenho de uma única atividade de cópia, se ainda não tiver atingido os limites superiores da taxa de transferência do seu ambiente – rede, armazenamento de dados de origem e armazenamento de dados de destino, você poderá executar várias atividades de cópia em paralelo usando construções de fluxo de controle ADF, como [para cada loop](control-flow-for-each-activity.md). Consulte [copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md), [migrar dados do Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md)ou [copiar em massa com](solution-template-bulk-copy-with-control-table.md) modelos de solução de tabela de controle como exemplo geral.

5. **Expanda a configuração para todo o conjunto de seus conjuntos de seus.** Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o pipeline para abranger todo o seu conjunto de todos.

## <a name="troubleshoot-copy-activity-performance"></a>Solucionar problemas de desempenho da atividade de cópia

Siga as [etapas de ajuste de desempenho](#performance-tuning-steps) para planejar e conduzir o teste de desempenho para seu cenário. E saiba como solucionar o problema de desempenho de cada execução da atividade de cópia no Azure Data Factory de [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Copiar recursos de otimização de desempenho

O Azure Data Factory fornece os seguintes recursos de otimização de desempenho:

- [Unidades de integração de dados](#data-integration-units)
- [Escalabilidade do tempo de execução de integração auto-hospedado](#self-hosted-integration-runtime-scalability)
- [Cópia paralela](#parallel-copy)
- [Cópia em etapas](#staged-copy)

### <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no Azure Data Factory. A unidade de integração de dados só se aplica ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não ao [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime). [Saiba mais](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade do tempo de execução de integração auto-hospedado

Para hospedar o aumento da carga de trabalho simultânea ou para obter um melhor desempenho, você pode escalar ou escalar horizontalmente o Integration Runtime hospedado internamente. [Saiba mais](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Cópia paralela

Você pode definir a cópia paralela para indicar o paralelismo que você deseja que a atividade de cópia use. Você pode considerar essa propriedade como o número máximo de threads na atividade de cópia que leu da origem ou gravar em armazenamentos de dados de coletor em paralelo. [Saiba mais](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. [Saiba mais](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Próximas etapas
Confira os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md)
- [Recursos de otimização de desempenho da atividade de cópia](copy-activity-performance-features.md)
- [Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
