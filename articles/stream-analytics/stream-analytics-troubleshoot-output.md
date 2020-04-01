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
ms.openlocfilehash: 305632a0faa1eb7e217e86d36c5159e557df7aaf
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409262"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Solucionar problemas de saídas do Azure Stream Analytics

Este artigo descreve problemas comuns com conexões de saída do Azure Stream Analytics, como solucionar problemas de saída e como corrigir os problemas. Muitas etapas de solução de problemas exigem que os registros de diagnóstico sejam habilitados para o seu trabalho de Stream Analytics. Se você não tiver registros de diagnóstico ativados, consulte [Solucionar problemas do Azure Stream Analytics usando registros de diagnóstico .](stream-analytics-job-diagnostic-logs.md)

## <a name="output-not-produced-by-job"></a>Saída não produzida pelo trabalho

1.  Verifique a conectividade com as saídas usando o botão **Testar Conexão** para cada uma das saídas.

2.  Veja [**as métricas de monitoramento**](stream-analytics-monitoring.md) na guia **Monitor.** Como os valores são agregados, as métricas são atrasadas em poucos minutos.
   * Se os Eventos de Entrada forem maiores que 0, o trabalho é capaz de ler dados de entrada. Se os Eventos de Entrada não forem maiores que 0, então há um problema com a entrada do trabalho. Consulte ['Solucionar problemas' nas conexões de entrada](stream-analytics-troubleshoot-input.md) para saber como solucionar problemas de conexão de entrada.
   * Se os erros de conversão de dados forem maiores que 0 e subir, consulte [erros de dados do Azure Stream Analytics](data-errors.md) para obter informações detalhadas sobre erros de conversão de dados.
   * Se os erros de tempo de execução forem maiores que 0, seu trabalho pode receber dados, mas está gerando erros ao processar a consulta. Para encontrar os erros, acesse os [Logs de Auditoria](../azure-resource-manager/management/view-activity-logs.md) e filtre o status *Com Falha*.
   * Se InputEvents for maior que 0 e OutputEvents for igual a 0, um dos seguintes é verdadeiro:
      * O processamento de consulta resultou em zero evento de saída.
      * Eventos ou campos podem estar mal formados, resultando em saída zero após o processamento da consulta.
      * O trabalho não pôde enviar dados por push para o coletor de saída por motivos de conectividade ou autenticação.

   Em todos os casos de erro mencionados anteriormente, as mensagens do log de operações explicam os detalhes adicionais (incluindo o que está acontecendo), exceto nos casos em que a lógica da consulta filtrou todos os eventos. Se o processamento de vários eventos gerar erros, os erros serão agregados a cada 10 minutos.

## <a name="job-output-is-delayed"></a>A saída do trabalho está atrasada

### <a name="first-output-is-delayed"></a>A primeira saída está atrasada

Quando um trabalho do Stream Analytics é iniciado, os eventos de entrada são lidos, mas pode haver um atraso na saída produzida em determinadas circunstâncias.

Valores de hora grande em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir saída correta sobre as janelas de tempo grande, o trabalho de streaming é inicializado lendo dados do último tempo possível (até sete dias atrás) para preencher a janela de tempo. Durante esse período, nenhuma saída é produzida até que a leitura de recuperação do atraso dos eventos de entrada pendentes seja concluída. Esse problema pode surgir quando o sistema atualiza os trabalhos de streaming, assim, reiniciando o trabalho. Essas atualizações geralmente ocorrem uma vez a cada dois meses.

Portanto, use critério ao criar a consulta do Stream Analytics. Se você usar uma janela de tempo grande (mais do que várias horas, até sete dias) para elementos temporal na sintaxe de consulta do trabalho, isso pode causar um atraso na primeira saída quando o trabalho é iniciado ou reiniciado.  

Uma mitigação para esse tipo de primeiro atraso de saída é usar técnicas de paralelização de consulta (particionamento de dados), ou adicionar mais unidades de Streaming para melhorar a taxa de transferência até o trabalho de captura.  Para obter mais informações, consulte [Considerações ao criar trabalhos do Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Esses fatores afetam a pontualidade da primeira saída que é gerada:

1. Uso de agregações em janela (GROUP BY Em cascata, Salto e Janelas deslizantes)
   - Para agregações de janela em cascata ou de salto, os resultados são gerados no final do período de tempo da janela.
   - Para uma janela deslizante, os resultados são gerados quando um evento entra ou sai da janela deslizante.
   - Se você estiver planejando usar a janela de tamanho grande (> 1 hora), é melhor escolher janela de salto ou deslizante para que você possa ver a saída com mais frequência.

2. Junções temporais (JOIN com DATEDIFF)
   - As correspondências serão geradas como quando chegarem a ambos os lados dos eventos correspondentes.
   - Dados que não tem uma correspondência (JUNÇÃO EXTERNA ESQUERDA) são gerados no final da janela DATEDIFF em relação a cada evento no lado esquerdo.

3. Uso de funções analíticas temporais (ISFIRST, LAST e a LAG com LIMIT DURATION)
   - Para funções analíticas, a saída será gerada para cada evento, não há nenhum atraso.

### <a name="output-falls-behind"></a>A saída fica atrás

Durante a operação normal do trabalho, se você verificar que a saída do trabalho está atrasada (latência cada vez maior), você pode identificar as causas examinando esses fatores:
- Se o coletor de downstream está limitado
- Se o coletor de upstream está limitado
- Se a lógica de processamento da consulta está com uso intensivo de computação

Para ver os detalhes, no portal do Azure, selecione o trabalho de streaming e selecione o **Diagrama de trabalho**. Para cada entrada, ha uma métrica de evento de lista de pendências por partição. Se a métrica de evento de lista de pendências continuar crescendo, é um indicador de que os recursos do sistema estão restritos. Potencialmente é devido a limitação de coletor de saída ou alta utilização da CPU. Para obter mais informações sobre como usar o diagrama de trabalho, consulte [Depuração orientada a dados usando o diagrama de trabalho](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação de chave com a saída do Banco de Dados SQL do Azure

Ao configurar o Banco de Dados SQL do Azure como saída para um trabalho do Stream Analytics, o volume insere registros na tabela de destino. Em geral, o Azure Stream Analytics garante [pelo menos uma vez a entrega](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) para o coletor de saída, ainda pode-se obter [EOD (exactly-once-delivery)]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para saída do SQL quando a tabela do SQL tiver uma restrição exclusiva definida.

Quando as restrições de chave exclusivas são definidas na tabela SQL e existem registros duplicados sendo inseridos na tabela do SQL, o Azure Stream Analytics remove o registro duplicado. Ele divide os dados em lotes e recursivamente insere os lotes até que um único registro duplicado seja encontrado. Se o trabalho de streaming tem um número considerável de linhas duplicadas, ele divide e insere o processo para ignorar as duplicatas uma por uma, que é menos eficiente e demorado. Se visualizar várias mensagens de advertência de violação da chave no log de atividades na última hora, é provável que a saída do SQL esteja retardando o trabalho inteiro.

Para resolver esse problema, é necessário [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está causando a violação da chave, habilitando a opção IGNORE_DUP_KEY. Habilitar essa opção permite que os valores duplicados sejam ignorados pelo SQL durante as inserções em massa e o SQL do Azure simplesmente produz uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produz mais erros de violação de chave primária.

Observe as observações a seguir ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível definir IGNORE_DUP_KEY em uma chave primária ou uma restrição exclusiva que utiliza ALTER INDEX, sendo necessário remover e recriar o índice.  
* É possível definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição PRIMARY KEY/UNIQUE e criada utilizando a definição CREATE INDEX ou INDEX.  

* IGNORE_DUP_KEY não é aplicável para índices de repositório de coluna porque não é possível impor a exclusividade desses índices.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Os nomes das colunas são minúsculos pelo Azure Stream Analytics
Ao usar o nível de compatibilidade original (1.0), o Azure Stream Analytics costumava alterar nomes de colunas para minúsculas. Esse comportamento foi corrigido em níveis posteriores de compatibilidade. Para preservar o caso, aconselhamos os clientes a passar para o nível de compatibilidade 1.1 e posterior. Você pode encontrar mais informações sobre [o nível de compatibilidade para trabalhos do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente [nosso fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
