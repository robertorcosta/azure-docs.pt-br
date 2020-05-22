---
title: Streaming de dados no SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre streaming de dados no SQL do Azure no Edge (versão prévia)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594505"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de dados no SQL do Azure no Edge (versão prévia)

O SQL do Azure no Edge (versão prévia) fornece duas opções diferentes para implementar o streaming de dados. 

1. Implantando trabalhos do Edge do Azure Stream Analytics criados no Azure. Para obter mais informações sobre como implantar trabalhos do Edge do Azure Stream Analytics no SQL do Azure no Edge, consulte [Implantar trabalhos do Azure Stream Analytics](deploy-dacpac.md).
2. Usando o novo recurso de **streaming de T-SQL** para criar trabalhos de streaming no SQL do Azure no Edge, sem a necessidade de configurar trabalhos de streaming no Azure. 

Embora seja possível usar as duas opções para implementar o streaming de dados no SQL do Azure no Edge, é altamente recomendável usar apenas uma. Quando se usam ambas, pode haver possíveis condições de corrida que afetam o funcionamento das operações de streaming de dados.

O restante deste documento refere-se ao novo recurso, o **streaming do T-SQL**, que fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar grandes volumes de dados de transmissão rápida de várias fontes simultaneamente. *Streaming de T-SQL* é criado usando o mesmo mecanismo de streaming de alto desempenho que capacita o [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) no Microsoft Azure e dá suporte a um conjunto semelhante de recursos oferecidos pelo Azure Stream Analytics em execução na borda.

Assim como ocorre com o Azure Stream Analytics, o Streaming de T-SQL permite o reconhecimento de padrões e relações nas informações extraídas de várias fontes de entrada de IoT, incluindo dispositivos, sensores e aplicativos. Esses padrões podem ser usados para disparar ações e iniciar fluxos de trabalho, como criação de alertas, informações de feed para uma solução de relatórios ou visualização, ou para armazenar dados para uso posterior. 

Os cenários a seguir são exemplos de quando você pode usar o streaming de T-SQL:

* Analisar streams de telemetria em tempo real de dispositivos IoT.
* Análise em tempo real de dados gerados de veículos autônomos e sem motorista.
* Monitoramento remoto e manutenção preditiva de ativos industriais ou de fabricação de alto valor.
* Detecção de anomalias e/ou reconhecimento de padrão das leituras do sensor de IoT em um farm de agricultura ou de energia.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o Streaming de T-SQL?

O streaming de T-SQL funciona exatamente da mesma maneira que o [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Por exemplo, ele usa o conceito de trabalhos de streaming para o processamento de streaming de dados em tempo real. 

Um trabalho do Stream Analytics consiste em:

- Entrada de fluxo – uma entrada de fluxo define as conexões com uma fonte de dados da qual ler o fluxo de dados. No momento, o SQL do Azure no Edge suporta os seguintes tipos de entrada de fluxo:
    - Hub do Edge
    - Kafka – No momento, o suporte para entradas Kafka só está disponível nas versões Intel/AMD64 do SQL do Azure no Edge.

- Saída de fluxo – Uma saída de fluxo define as conexões com uma fonte de dados na qual gravar o fluxo de dados. No momento, o SQL do Azure no Edge suporta os seguintes tipos de saída de fluxo:
    - Hub do Edge
    - SQL – A saída SQL pode ser um banco de dados local dentro da instância do Banco de Dados SQL no Edge ou um SQL Server remoto ou um Banco de dados SQL do Azure. 
    - Armazenamento do Blobs do Azure

- Consulta de fluxo – A consulta de fluxo define a transformação, as agregações, o filtro, a classificação e as junções que precisam ser aplicadas ao fluxo de entrada antes de ser gravado na saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta usada pelo Azure Stream Analytics. Para obter mais informações sobre a linguagem de consulta do Azure Stream Analytics, consulte [Linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Ao contrário do Azure Stream Analytics, o streaming de T-SQL no momento não oferece suporte ao [uso de dados de referência para pesquisas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) nem ao [uso de UDF e UDA no trabalho de stream](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> O Streaming de T-SQL só suporta um subconjunto da área de superfície da linguagem suportado pelo Azure Stream Analytics. Para obter mais informações sobre a linguagem de consulta do Azure Stream Analytics, leia [Linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As seguintes limitações e restrições aplicam-se ao Streaming de T-SQL. 

- Só um trabalho de streaming pode estar ativo em qualquer momento. Os trabalhos que já estão em execução devem ser interrompidos antes do início de outro trabalho.
- Cada execução de trabalho de streaming é de thread único. Se o trabalho de streaming contiver várias consultas, cada uma será avaliada em ordem de série.

## <a name="next-steps"></a>Próximas etapas

- [Criar um trabalho de Stream Analytics no SQL do Azure no Edge (visualização) ](create-stream-analytics-job.md)
- [Como exibir metadados associados a trabalhos de streaming no SQL do Azure no Edge (visualização) ](streaming-catalog-views.md)
- [Criar um stream externo](create-external-stream-transact-sql.md)