---
title: Solucionar problemas de consultas do Azure Stream Analytics
description: Este artigo descreve as técnicas para solucionar problemas das suas consultas nos trabalhos do Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844405"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Solucionar problemas de consultas do Azure Stream Analytics

Este artigo descreve problemas comuns com o desenvolvimento de consultas do Stream Analytics e como solucioná-los.

## <a name="query-is-not-producing-expected-output"></a>A consulta não está produzindo a saída esperada
1.  Examine os erros testando localmente:
    - No portal Azure, na guia **Consulta,** selecione **Teste**. Use os dados de exemplo baixado para [testar a consulta](stream-analytics-test-query.md). Examine os erros e tente corrigi-los.   
    - Você também pode [testar sua consulta localmente](stream-analytics-live-data-local-testing.md) usando ferramentas do Azure Stream Analytics para visual studio ou [visual studio code](visual-studio-code-local-run-live-input.md). 

2.  [Depurações passo a passo localmente usando o diagrama de trabalho](debug-locally-using-job-diagram.md) nas ferramentas do Azure Stream Analytics para o Visual Studio. O diagrama de trabalho é para mostrar como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de várias etapas de consulta e, finalmente, saída para afundantes. Cada etapa de consulta é mapeada para um conjunto de resultados temporário definido no script usando a declaração WITH. Você pode visualizar os dados, bem como as métricas em cada etapa de consulta em cada resultado intermediário definido para encontrar a fonte do problema.
    ![Resultado da visualização do diagrama do trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Se você usar [**Carimbo de Data/Hora Por**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), verifique se os eventos têm carimbos de data/hora maiores que a [hora de início do trabalho](stream-analytics-out-of-order-and-late-events.md).

4.  Elimine armadilhas comuns, como:
    - Uma cláusula [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) na consulta filtrou todos os eventos, impedindo que uma saída seja gerada.
    - Uma função [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) falha, causando a falha do trabalho. Nesse caso, para evitar falhas de conversão de tipo, use [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - Ao usar funções de janela, aguarde a duração de toda a janela para ver uma saída da consulta.
    - O carimbo de data/hora de eventos precede a hora de início do trabalho e, portanto, os eventos são removidos.

5.  Verifique se as políticas de ordenação de eventos estão configuradas conforme o esperado. Acesse as **Configurações** e selecione [**Ordenação de Eventos**](stream-analytics-out-of-order-and-late-events.md). A política *não* é aplicada quando você usa o botão **Testar** para testar a consulta. Esse resultado é uma das diferenças entre o teste no navegador comparado à execução do trabalho em produção. 

6. Depurar usando os logs de auditoria e de diagnóstico:
    - Use os [Logs de Auditoria](../azure-resource-manager/resource-group-audit.md) e filtre para identificar e depurar erros.
    - Use os [logs de diagnóstico do trabalho](stream-analytics-job-diagnostic-logs.md) para identificar e depurar erros.

## <a name="job-is-consuming-too-many-streaming-units"></a>O trabalho consome muitas Unidades de Streaming
Este artigo mostra como tirar proveito da paralelização no Azure Stream Analytics. Você pode aprender a [dimensionar com paralelização de consultas](stream-analytics-parallelization.md) de trabalhos do Stream Analytics configurando partições de entrada e ajustando a definição da consulta de análise.

## <a name="debug-queries-progressively"></a>Depurar consultas progressivamente

No processamento de dados em tempo real, saber qual será a aparência dos dados no meio da consulta pode ser útil. Como as entradas ou etapas de um trabalho do Stream Analytics do Azure podem ser lidas várias vezes, você pode escrever instruções SELECT INTO extras. Ao fazer isso, dados intermediários são gerados no armazenamento que permitem a você inspecionar a exatidão dos dados, assim como as *variáveis de inspeção* fazem quando você depura um programa.

O exemplo de consulta a seguir em um trabalho do Stream Analytics do Azure tem uma entrada de fluxo, duas entradas de dados de referência e uma saída para o Armazenamento de Tabelas do Azure. A consulta une dados do hub de eventos e dois blobs de referência para obter informações de nome e categoria:

![No Stream Analytics, consulta SELECIONAR EM](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observe que o trabalho está em execução, mas não há eventos sendo gerados na saída. No bloco **Monitoramento**, mostrado aqui, você pode ver que a entrada está gerando dados, mas não dá para saber qual etapa de **JOIN** fez com que todos os eventos fossem removidos.

![Bloco de Monitoramento do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Nesse caso, é possível adicionar algumas instruções extras SELECT INTO para "registrar em log" os resultados intermediários de JOIN e os dados que são lidos da entrada.

Neste exemplo, adicionamos duas novas "saídas temporárias." Elas podem ser qualquer coletor desejado. Aqui, usamos o Armazenamento do Azure como um exemplo:

![Adicionar instruções SELECIONAR EM para a consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Assim, você pode reescrever a consulta desta forma:

![Consulta regravada de SELECIONAR EM do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora, reinicie o trabalho e deixe ele ser executado por alguns minutos. Em seguida, consulte temp1 e temp2 com o Cloud Explorer do Visual Studio para gerar as seguintes tabelas:

**tabela temp1**
![SELECT INTO temp1 tabela Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabela temp2**
![SELECIONE em consulta de fluxo de fluxo de tabela temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como você pode ver, temp1 e temp2 têm dados e a coluna de nome é preenchida corretamente em temp2. No entanto, como ainda não há dados na saída, algo está errado:

![Tabela output1 SELECIONAR EM sem dados da consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pela amostragem de dados, você pode ter quase certeza de que o problema é com o segundo JOIN. Você pode baixar os dados de referência do blob e dar uma olhada:

![SELECIONAR EM ref de consulta da tabela do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como você pode ver, o formato do GUID nos dados de referência é diferente do formato da coluna [from] em temp2. Por esse motivo, os dados não chegaram em output1 como esperado.

É possível corrigir o formato dos dados, carregá-los no blob de referência e tentar novamente:

![SELECIONAR EM tabela temporária da consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dessa vez, os dados na saída são formatados e preenchidos conforme esperado.

![SELECIONAR EM tabela final de consulta do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente [nosso fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
