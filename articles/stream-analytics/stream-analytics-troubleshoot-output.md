---
title: Solucionar problemas de saídas do Azure Stream Analytics
description: Este artigo descreve técnicas para solucionar problemas de conexões de entrada em trabalhos do Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147941"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Solucionar problemas de saídas do Azure Stream Analytics

Este artigo descreve problemas comuns com Azure Stream Analytics conexões de saída e como solucionar problemas de saída. Muitas etapas de solução de problemas exigem que recursos e outros logs de diagnóstico sejam habilitados para seu trabalho de Stream Analytics. Se você não tiver os logs de recursos habilitados, consulte [solucionar problemas Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>O trabalho não produz a saída

1. Verifique a conectividade com as saídas usando o botão **Testar Conexão** para cada uma das saídas.
1. Examine as [métricas de monitoramento](stream-analytics-monitoring.md) na guia **Monitor** . Como os valores são agregados, as métricas são atrasadas em alguns minutos.

   * Se o valor dos **eventos de entrada** for maior que zero, o trabalho poderá ler os dados de entrada. Se o valor de **eventos de entrada** não for maior que zero, haverá um problema com a entrada do trabalho. Consulte [solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md) para obter mais informações.
   * Se o valor dos **erros de conversão de dados** for maior que zero e a escalada, consulte [Azure Stream Analytics erros de dados](data-errors.md) para obter informações detalhadas sobre erros de conversão de dados.
   * Se o valor de **erros de tempo de execução** for maior que zero, seu trabalho receberá dados, mas gerará erros durante o processamento da consulta. Para localizar os erros, vá para os [logs de auditoria](../azure-resource-manager/management/view-activity-logs.md)e, em seguida, filtre o status **com falha** .
   * Se o valor dos **eventos de entrada** for maior que zero e o valor dos **eventos de saída** for igual a zero, uma das instruções a seguir será verdadeira:
      * O processamento da consulta resultou em zero eventos de saída.
      * Eventos ou campos podem estar malformados, resultando em uma saída zero após o processamento da consulta.
      * O trabalho não pôde enviar dados por push para o coletor de saída por motivos de conectividade ou autenticação.

   As mensagens de log de operações explicam detalhes adicionais, incluindo o que está acontecendo, exceto nos casos em que a lógica de consulta filtra todos os eventos. Se o processamento de vários eventos gerar erros, os erros são agregados a cada 10 minutos.

## <a name="the-first-output-is-delayed"></a>A primeira saída é atrasada

Quando um trabalho de Stream Analytics é iniciado, os eventos de entrada são lidos. No entanto, pode haver um atraso na saída, em determinadas circunstâncias.

Valores de hora grande em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir a saída correta em janelas de tempo grande, o trabalho de streaming lê os dados do último tempo possível para preencher a janela de tempo. Os dados podem ter até sete dias após. Nenhuma saída produz até que os eventos de entrada pendentes sejam lidos. Esse problema pode surgir quando o sistema atualizar os trabalhos de streaming. Quando ocorre uma atualização, o trabalho é reiniciado. Essas atualizações geralmente ocorrem uma vez a cada dois meses.

Use o critério ao criar sua consulta de Stream Analytics. Se você usar uma janela de tempo grande para elementos temporais na sintaxe de consulta do trabalho, isso poderá levar a um atraso na primeira saída quando o trabalho for iniciado ou reiniciado. Mais de algumas horas, até sete dias, é considerado uma janela de tempo grande.

Uma mitigação para esse tipo de atraso de primeira saída é usar técnicas de paralelização de consulta, como particionar os dados. Ou então, você pode adicionar mais unidades de streaming para melhorar a taxa de transferência até que o trabalho seja atualizado.  Para obter mais informações, consulte [considerações ao criar trabalhos de Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

Esses fatores afetam a linha do tempo da primeira saída:

* O uso de agregações em janela, como uma cláusula GROUP BY de em cascata, salto e janelas deslizantes:

  * Para agregações de janela em cascata ou de salto, os resultados são gerados no final do período de tempo da janela.
  * Para uma janela deslizante, os resultados são gerados quando um evento entra ou sai da janela deslizante.
  * Se você estiver planejando usar um tamanho de janela grande, como mais de uma hora, é melhor escolher uma janela de salto ou deslizante. Esses tipos de janela permitem ver a saída com mais frequência.

* O uso de junções temporais, como JOIN com DATEDIFF:
  * Corresponde à geração assim que ambos os lados dos eventos correspondidos chegam.
  * Os dados que não têm uma correspondência, como junção externa esquerda, são gerados no final da janela DATEDIFF para cada evento no lado esquerdo.

* O uso de funções analíticas temporais, como isprimeiro, LAST e LAG com limite de duração:
  * Para funções analíticas, a saída é gerada para cada evento. Não há nenhum atraso.

## <a name="the-output-falls-behind"></a>A saída fica atrás

Durante a operação normal de um trabalho, a saída pode ter períodos de latência mais longos e longos. Se a saída ficar por trás dessa forma, você poderá identificar as causas raiz examinando os seguintes fatores:

* Se o coletor de downstream está limitado
* Se o coletor de upstream está limitado
* Se a lógica de processamento da consulta está com uso intensivo de computação

Para ver os detalhes de saída, selecione o trabalho de streaming na portal do Azure e, em seguida, selecione **diagrama de trabalho**. Para cada entrada, há uma métrica de evento de pendência por partição. Se a métrica continuar aumentando, será um indicador de que os recursos do sistema estão restritos. O aumento é potencialmente devido à limitação do coletor de saída ou ao alto uso da CPU. Para obter mais informações, consulte [depuração controlada por dados usando o diagrama de trabalho](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação de chave com a saída do Banco de Dados SQL do Azure

Quando você configura um banco de dados SQL do Azure como saída para um trabalho Stream Analytics, ele insere registros em massa na tabela de destino. Em geral, Azure Stream Analytics garante a [entrega pelo menos uma vez](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) ao coletor de saída. Você ainda pode [obter uma entrega exatamente uma vez]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para uma saída SQL quando uma tabela SQL tem uma restrição UNIQUE definida.

Quando você configura restrições de chave exclusivas na tabela SQL, Azure Stream Analytics remove registros duplicados. Ele divide os dados em lotes e insere recursivamente os lotes até que um único registro duplicado seja encontrado. O processo de divisão e inserção ignora as duplicatas uma de cada vez. Para um trabalho de streaming que tem muitas linhas duplicadas, o processo é ineficiente e demorado. Se você vir várias mensagens de aviso de violação de chave no log de atividades da hora anterior, é provável que a saída do SQL esteja diminuindo o trabalho inteiro.

Para resolver esse problema, [Configure o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está causando a violação de chave habilitando a opção ignore_dup_key. Essa opção permite que o SQL ignore valores duplicados durante inserções em massa. O banco de dados SQL do Azure simplesmente produz uma mensagem de aviso em vez de um erro. Como resultado, Azure Stream Analytics não produz mais erros de violação de chave primária.

Observe as observações a seguir ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Você não pode definir IGNORE_DUP_KEY em uma chave primária ou uma restrição UNIQUE que usa ALTER INDEX. Você precisa descartar o índice e recriá-lo.  
* Você pode definir IGNORE_DUP_KEY usando ALTER INDEX para um índice exclusivo. Essa instância é diferente de uma restrição PRIMARY KEY/UNIQUE e é criada usando uma definição CREATE INDEX ou INDEX.  
* A opção IGNORE_DUP_KEY não se aplica a índices de repositório de coluna porque você não pode impor exclusividade neles.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Os nomes de coluna são minúsculos em Azure Stream Analytics (1,0)

Ao usar o nível de compatibilidade original (1,0), Azure Stream Analytics altera os nomes de coluna para minúsculas. Esse comportamento foi corrigido em níveis de compatibilidade posteriores. Para preservar o caso, vá para o nível de compatibilidade 1,1 ou posterior. Para obter mais informações, consulte [nível de compatibilidade para trabalhos de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
