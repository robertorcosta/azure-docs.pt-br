---
title: Copiar o desempenho da atividade e o guia de escalabilidade
description: Conheça os principais fatores que afetam o desempenho da movimentação de dados na Fábrica de Dados do Azure quando você usa a atividade de cópia.
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
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261392"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Copiar o desempenho da atividade e o guia de escalabilidade

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

Se você deseja realizar uma migração de dados em larga escala do data lake ou do enterprise data warehouse (EDW) para o Azure, ou se você deseja ingerir dados em escala de diferentes fontes para o Azure para análise de big data, é fundamental alcançar o desempenho ideal e Escalabilidade.  A Azure Data Factory fornece um mecanismo de desempenho, resiliência e custo-benefício para ingerir dados em escala, tornando-o um ótimo ajuste para engenheiros de dados que procuram construir pipelines de ingestão de dados altamente executáveis e escaláveis.

Depois de ler este artigo, você poderá responder as seguintes perguntas:

- Que nível de desempenho e escalabilidade posso alcançar usando a atividade de cópia do ADF para cenários de migração de dados e ingestão de dados?

- Quais passos devo tomar para ajustar o desempenho da atividade de cópia do ADF?
- Quais botões de otimização perf do ADF posso utilizar para otimizar o desempenho para uma única execução de atividade de cópia?
- Quais outros fatores fora do ADF considerar ao otimizar o desempenho da cópia?

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte a visão geral da [atividade de cópia](copy-activity-overview.md) antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade alcançáveis usando ADF

O ADF oferece uma arquitetura sem servidor que permite o paralelismo em diferentes níveis, o que permite que os desenvolvedores construam pipelines para utilizar totalmente sua largura de banda de rede, bem como IOPS de armazenamento e largura de banda para maximizar o throughput de movimentação de dados para o seu ambiente.  Isso significa que o throughput que você pode alcançar pode ser estimado medindo o rendimento mínimo oferecido pelo armazenamento de dados de origem, o armazenamento de dados de destino e a largura de banda da rede entre a origem e o destino.  A tabela abaixo calcula a duração da cópia com base no tamanho dos dados e no limite de largura de banda do seu ambiente. 

| Tamanho dos dados / <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min.    | 1,4 min.   | 0,3 min.   | 0,1 min.  | 0,03 min. | 0,01 min. | 0,0 min.   |
| **10 GB**                   | 27,3 min.   | 13,7 min.  | 2,7 min.   | 1,3 min.  | 0,3 min.  | 0,1 min.  | 0,03 min.  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0,05 hrs | 0,02 hrs | 0,0 hrs   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0,05 hrs  |
| **10 TB**                   | 19.4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0,02 dias |
| **100 TB**                  | 194.2 dias | 97.1 dias | 19.4 dias | 9,7 dias | 1,9 dias | 1 dia    | 0,2 dias  |
| **1 PB**                    | 64,7 milhões    | 32,4 milhões   | 6,5 milhões    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 milhões   | 323,6 milhões  | 64,7 milhões   | 31,6 mo  | 6,5 milhões   | 3,2 mo   | 0,6 mo    |

A cópia ADF é escalável em diferentes níveis:

![como as escalas de cópia do ADF](media/copy-activity-performance/adf-copy-scalability.png)

- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [For Each loop](control-flow-for-each-activity.md).
- Uma única atividade de cópia pode tirar proveito de recursos de computação escaláveis: ao usar o Azure Integration Runtime, você pode especificar [até 256 DIUs](#data-integration-units) para cada atividade de cópia de forma sem servidor; ao usar o Executtime de Integração auto-hospedado, você pode escalar manualmente a máquina ou dimensionar para várias[máquinas (até 4 nós)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)e uma única atividade de cópia irá particionar seu conjunto de arquivos em todos os nós.
- Uma única atividade de cópia é lida e grava no armazenamento de dados usando vários segmentos [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho

Tome essas etapas para ajustar o desempenho do serviço azure Data Factory com a atividade de cópia.

1. **Pegue um conjunto de dados de teste e estabeleça uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia contra uma amostra de dados representativa. O conjunto de dados escolhido deve representar seus padrões típicos de dados (estrutura de pasta, padrão de arquivo, esquema de dados e assim por diante), e é grande o suficiente para avaliar o desempenho da cópia, por exemplo, leva 10 minutos ou mais para a atividade de cópia ser concluída. Coletar detalhes de execução e características de desempenho após [o monitoramento da atividade de cópia](copy-activity-monitoring.md).

2. **Como maximizar o desempenho de uma única atividade de cópia:**

   Para começar, recomendamos que você primeiro maximize o desempenho usando uma única atividade de cópia.

   - **Se a atividade de cópia estiver sendo executada em um Tempo de execução de integração do Azure:** comece com os valores padrão para [DIU (Data Integration Units, unidades de integração de dados)](#data-integration-units) e configurações [de cópia paralela.](#parallel-copy) 

   - **Se a atividade de cópia estiver sendo executada em um tempo de execução de integração auto-hospedado:** recomendamos que você use uma máquina dedicada separada do servidor que hospeda o armazenamento de dados para hospedar o tempo de execução da integração. Comece com valores padrão para a configuração [de cópia paralela](#parallel-copy) e use um único nó para o IR auto-hospedado.  

   Execute uma execução de teste de desempenho e tome uma nota do desempenho alcançado, bem como dos valores reais usados como DIUs e cópias paralelas. Consulte o [monitoramento de atividades de cópia](copy-activity-monitoring.md) sobre como coletar resultados de execução e configurações de desempenho usadas e saiba como [solucionar o desempenho da atividade](copy-activity-performance-troubleshooting.md) de cópia para identificar e resolver o gargalo. 

   Iterar para realizar testes adicionais de desempenho após a solução de problemas e orientação de ajuste. Uma vez que a execução de atividade de cópia única não pode obter um melhor throughput, considere maximizar o throughput agregado executando várias cópias simultaneamente referindo-se à etapa 3.


3. **Como maximizar o throughput agregado executando várias cópias simultaneamente:**

   Agora que você maximizou o desempenho de uma única atividade de cópia, se você ainda não atingiu os limites superiores de throughput do seu ambiente – rede, armazenamento de dados de origem e armazenamento de dados de destino – você pode executar várias atividades de cópia em paralelo usando construções de fluxo de controle ADF, como [For Each loop](control-flow-for-each-activity.md). Consulte [copiar arquivos de vários contêineres,](solution-template-copy-files-multiple-containers.md) [migrar dados do Amazon S3 para ODLS Gen2](solution-template-migration-s3-azure.md)ou copiar em massa com [modelos de](solution-template-bulk-copy-with-control-table.md) solução de tabela de controle como exemplo geral.

5. **Expanda a configuração para todo o conjunto de dados.** Quando você está satisfeito com os resultados de execução e desempenho, você pode expandir a definição e o pipeline para cobrir todo o seu conjunto de dados.

## <a name="troubleshoot-copy-activity-performance"></a>Solucionar problemas no desempenho da atividade de cópia

Siga as [etapas de ajuste de desempenho](#performance-tuning-steps) para planejar e realizar o teste de desempenho para o seu cenário. E aprenda como solucionar problemas do problema de desempenho de cada série de atividades de cópia na Fábrica de Dados do Azure a partir do desempenho da atividade de [cópia de solução de problemas](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Recursos de otimização de desempenho de cópia

A Fábrica de Dados do Azure fornece os seguintes recursos de otimização de desempenho:

- [Unidades de Integração de Dados](#data-integration-units)
- [Escalabilidade de tempo de execução de integração auto-hospedada](#self-hosted-integration-runtime-scalability)
- [Cópia paralela](#parallel-copy)
- [Cópia em etapas](#staged-copy)

### <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa a potência (uma combinação de alocação de recursos de CPU, memória e rede) de uma única unidade na Fábrica de Dados Do Azure. A Unidade de Integração de Dados só se aplica ao [tempo de execução de integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não no [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime). [Saiba mais](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade de tempo de execução de integração auto-hospedada

Para hospedar uma carga de trabalho simultânea crescente ou para obter um desempenho mais alto, você pode escalar ou dimensionar o Tempo de Execução de Integração Auto-hospedado. [Saiba mais](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Cópia paralela

Você pode definir uma cópia paralela para indicar o paralelismo que deseja que a atividade de cópia use. Você pode pensar nesta propriedade como o número máximo de threads dentro da atividade de cópia que lê de sua fonte ou gravar para seus armazenamentos de dados da pia em paralelo. [Saiba mais](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. [Saiba mais](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Solucionar problemas no desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md)
- [Recursos de otimização do desempenho da atividade de cópia](copy-activity-performance-features.md)
- [Use a Fábrica de Dados do Azure para migrar dados do seu lago de dados ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
