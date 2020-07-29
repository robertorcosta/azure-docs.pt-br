---
title: Streaming de dados no SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre streaming de dados no Azure SQL Edge (versão prévia).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 866c74fbdfcfcef7cbb7d6cddb360c4265a2f776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669606"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de dados no SQL do Azure no Edge (versão prévia)

O Azure SQL Edge (visualização) fornece as seguintes opções para implementar o streaming de dados: 

- Implantando Azure Stream Analytics trabalhos de borda criados no Azure. Para obter mais informações, consulte [implantar trabalhos de Azure Stream Analytics](deploy-dacpac.md).
- Usando o streaming do T-SQL para criar trabalhos de streaming no Azure SQL Edge, sem a necessidade de configurar trabalhos de streaming no Azure. 

Embora seja possível usar as duas opções para implementar o streaming de dados no Azure SQL Edge, você deve usar apenas um deles. Quando você estiver usando ambos, pode haver condições de corrida que afetam o funcionamento das operações de streaming de dados.

O streaming T-SQL é o foco deste artigo. Ele fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar grandes volumes de dados de streaming rápido de várias fontes, simultaneamente. O streaming T-SQL é criado usando o mesmo mecanismo de streaming de alto desempenho que capacita [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) em Microsoft Azure. O recurso dá suporte a um conjunto semelhante de recursos oferecidos pelo Azure Stream Analytics em execução na borda.

Assim como ocorre com Stream Analytics, o streaming do T-SQL reconhece padrões e relações nas informações extraídas de várias fontes de entrada de IoT, incluindo dispositivos, sensores e aplicativos. Você pode usar esses padrões para disparar ações e iniciar fluxos de trabalho. Por exemplo, você pode criar alertas, alimentar informações a uma solução de relatório ou visualização ou armazenar os dados para uso posterior. 

O streaming T-SQL pode ajudá-lo:

* Analisar streams de telemetria em tempo real de dispositivos IoT.
* Use análise em tempo real de dados gerados de veículos autônomos e de driver.
* Use o monitoramento remoto e a manutenção preditiva de ativos de produção ou industrial de alto valor.
* Use a detecção de anomalias e o reconhecimento de padrões das leituras do sensor IoT em um agricultura ou em um farm de energia.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o streaming do T-SQL?

O streaming T-SQL funciona exatamente da mesma maneira que [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Por exemplo, ele usa o conceito de *trabalhos* de streaming para o processamento de streaming de dados em tempo real. 

Um trabalho do Stream Analytics consiste em:

- **Entrada de fluxo**: define as conexões com uma fonte de dados da qual ler o fluxo de dados. No momento, o SQL do Azure no Edge suporta os seguintes tipos de entrada de fluxo:
    - Hub do Edge
    - Kafka (o suporte para entradas de Kafka está disponível atualmente apenas nas versões Intel/AMD64 do Azure SQL Edge.)

- **Saída de fluxo**: define as conexões com uma fonte de dados na qual gravar o fluxo de dados. No momento, o SQL do Azure no Edge suporta os seguintes tipos de saída de fluxo:
    - Hub do Edge
    - SQL (a saída SQL pode ser um banco de dados local dentro da instância do Azure SQL Edge ou um SQL Server remoto ou um banco de dados SQL do Azure.) 
    - Armazenamento de Blobs do Azure

- **Consulta de fluxo**: define a transformação, as agregações, o filtro, a classificação e as junções a serem aplicadas ao fluxo de entrada, antes de serem gravadas na saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta usada pelo Stream Analytics. Para obter mais informações, consulte [Stream Analytics linguagem de consulta](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> O streaming T-SQL, diferente do Stream Analytics, atualmente não dá suporte ao [uso de dados de referência para pesquisas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ou ao [uso de UDF e UDA em um trabalho de fluxo](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> O streaming T-SQL dá suporte apenas a um subconjunto da área de superfície da linguagem com suporte pelo Stream Analytics. Para obter mais informações, consulte [Stream Analytics linguagem de consulta](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As limitações e restrições a seguir se aplicam ao streaming T-SQL. 

- Somente um trabalho de streaming pode estar ativo em qualquer momento específico. Os trabalhos que já estão em execução devem ser interrompidos antes de iniciar outro trabalho.
- Cada execução de trabalho de streaming é de thread único. Se o trabalho de streaming contiver várias consultas, cada consulta será avaliada em ordem de série.

## <a name="next-steps"></a>Próximas etapas

- [Criar um trabalho de Stream Analytics no SQL do Azure no Edge (visualização) ](create-stream-analytics-job.md)
- [Como exibir metadados associados a trabalhos de streaming no SQL do Azure no Edge (visualização) ](streaming-catalog-views.md)
- [Criar fluxo externo](create-external-stream-transact-sql.md)