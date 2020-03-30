---
title: Consultas de monitor
titleSuffix: Azure Cognitive Search
description: Monitore as métricas de consulta para desempenho e throughput. Coletar e analisar entradas de seqüência de consultas em registros de diagnóstico.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462507"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorar solicitações de consulta no Azure Cognitive Search

Este artigo explica como medir o desempenho e o volume da consulta usando métricas e registro de diagnóstico. Ele também explica como coletar os termos de entrada usados em consultas - informações necessárias quando você precisa avaliar a utilidade e eficácia do seu corpus de pesquisa.

Os dados históricos que alimentam métricas são preservados por 30 dias. Para maior retenção ou para relatar dados operacionais e seqüências de consultas, certifique-se de habilitar uma [configuração de diagnóstico](search-monitor-logs.md) que especifica uma opção de armazenamento para eventos e métricas registradas persistentes.

As condições que maximizam a integridade da medição de dados incluem:

+ Use um serviço de cobrança (um serviço criado no nível Básico ou Padrão). O serviço gratuito é compartilhado por vários assinantes, o que introduz uma certa quantidade de volatilidade à medida que as cargas mudam.

+ Use uma única réplica e partição, se possível, para criar um ambiente contido e isolado. Se você usar várias réplicas, as métricas de consulta são médias em vários nós, o que pode diminuir a precisão dos resultados. Da mesma forma, várias partições significam que os dados são divididos, com o potencial de que algumas partições possam ter dados diferentes se a indexação também estiver em andamento. Ao ajustar o desempenho da consulta, um único nó e partição dá um ambiente mais estável para testes.

> [!Tip]
> Com código suplementar do lado do cliente e insights de aplicativos, você também pode capturar dados por clique através de informações mais profundas sobre o que atrai o interesse dos usuários do seu aplicativo. Para saber mais, confira [Análise de tráfego de pesquisa](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Volume de consulta (QPS)

O volume é medido como **Perguntas de Pesquisa por Segundo** (QPS), uma métrica incorporada que pode ser relatada como valores médios, de contagem, mínimos ou máximos de consultas que são executadas dentro de uma janela de um minuto. Intervalos de um minuto (TimeGrain = "PT1M") para métricas são fixados dentro do sistema.

É comum que consultas sejam executadas em milissegundos, então apenas consultas que medem como segundos aparecerão em métricas.

| Tipo de agregação | Descrição |
|------------------|-------------|
| Média | O número médio de segundos dentro de um minuto durante o qual a execução da consulta ocorreu.|
| Contagem | O número de métricas emitidas para o registro dentro do intervalo de um minuto. |
| Máximo | O maior número de consultas de pesquisa por segundo registrado durante um minuto. |
| Mínimo | O menor número de consultas de pesquisa por segundo registrado durante um minuto.  |
| SUM | A soma de todas as consultas executadas dentro de um minuto.  |

Por exemplo, dentro de um minuto, você pode ter um padrão como este: um segundo de alta carga que é o máximo para SearchQueriesPerSecond, seguido por 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Outro exemplo: se um nó emite 100 métricas, onde o valor de cada métrica é 40, então "Contagem" é 100, "Soma" é 4000, "Média" é 40 e "Max" é 40.

## <a name="query-performance"></a>Desempenho de consulta

O desempenho de consulta em todo o serviço é medido como latência de pesquisa (quanto tempo uma consulta leva para ser concluída) e consultas estranguladas que foram descartadas como resultado da contenção de recursos.

### <a name="search-latency"></a>Latência de pesquisa

| Tipo de agregação | Latency | 
|------------------|---------|
| Média | Duração média da consulta em milissegundos. | 
| Contagem | O número de métricas emitidas para o registro dentro do intervalo de um minuto. |
| Máximo | Consulta mais longa da amostra. | 
| Mínimo | Consulta de execução mais curta na amostra.  | 
| Total | Tempo total de execução de todas as consultas na amostra, executando dentro do intervalo (um minuto).  |

Considere o seguinte exemplo de métricas de **Latência** de Pesquisa: 86 consultas foram amostradas, com duração média de 23,26 milissegundos. Um mínimo de 0 indica que algumas consultas foram retiradas. A consulta mais longa levou 1000 milissegundos para ser concluída. O tempo total de execução foi de 2 segundos.

![Agregações de latência](./media/search-monitor-usage/metrics-latency.png "Agregações de latência")

### <a name="throttled-queries"></a>Consultas estranguladas

Consultas estranguladas referem-se a consultas que são descartadas em vez de processo. Na maioria dos casos, o estrangulamento é uma parte normal da execução do serviço.  Não é necessariamente uma indicação de que há algo errado.

O estrangulamento ocorre quando o número de solicitações atualmente processadas excede os recursos disponíveis. Você pode ver um aumento nas solicitações estranguladas quando uma réplica é retirada da rotação ou durante a indexação. Tanto as solicitações de consulta quanto de indexação são tratadas pelo mesmo conjunto de recursos.

O serviço determina se deve retirar solicitações com base no consumo de recursos. A porcentagem de recursos consumidos em memória, CPU e IO de disco é média durante um período de tempo. Se esse percentual exceder um limite, todas as solicitações ao índice serão estranguladas até que o volume de solicitações seja reduzido. 

Dependendo do seu cliente, uma solicitação estrangulada pode ser indicada desta forma:

+ Um serviço retorna um erro "Você está enviando muitas solicitações. Tente novamente mais tarde.” 
+ Um serviço retorna um código de erro 503 indicando que o serviço está indisponível no momento. 
+ Se você estiver usando o portal (por exemplo, Search Explorer), a consulta será descartada silenciosamente e você precisará clicar em Pesquisar novamente.

Para confirmar as consultas estranguladas, use a métrica **de consultas de pesquisa estrangulada.** Você pode explorar métricas no portal ou criar uma métrica de alerta conforme descrito neste artigo. Para consultas que foram descartadas dentro do intervalo de amostragem, use *Total* para obter a porcentagem de consultas que não foram executadas.

| Tipo de agregação | Limitação |
|------------------|-----------|
| Média | A porcentagem de consultas caiu dentro do intervalo. |
| Contagem | O número de métricas emitidas para o registro dentro do intervalo de um minuto. |
| Máximo | A porcentagem de consultas caiu dentro do intervalo.|
| Mínimo | A porcentagem de consultas caiu dentro do intervalo. |
| Total | A porcentagem de consultas caiu dentro do intervalo. |

Para **a Porcentagem de Consultas de Pesquisa Estrangulada,** mínimo, máximo, médio e total, todos têm o mesmo valor: a porcentagem de consultas de pesquisa que foram estranguladas, a partir do número total de consultas de pesquisa durante um minuto.

Na captura de tela a seguir, o primeiro número é a contagem (ou número de métricas enviadas ao registro). Agregações adicionais, que aparecem na parte superior ou quando pairam sobre a métrica, incluem média, máximo e total. Nesta amostra, nenhum pedido foi descartado.

![Agregações estranguladas](./media/search-monitor-usage/metrics-throttle.png "Agregações estranguladas")

## <a name="explore-metrics-in-the-portal"></a>Explorar métricas no portal

Para uma rápida olhada nos números atuais, a guia **Monitoramento** na página Visão Geral do serviço mostra três métricas (**Latência de**pesquisa , **consultas de pesquisa por segundo (por unidade de pesquisa)**, **Porcentagem de consultas de pesquisa estranguladas**) sobre intervalos fixos medidos em horas, dias e semanas, com a opção de alterar o tipo de agregação.

Para uma exploração mais profunda, abra o explorador de métricas do menu **Monitorando** para que você possa camada, zoom e visualização de dados para explorar tendências ou anomalias. Saiba mais sobre o metrics explorer completando este [tutorial sobre a criação de um gráfico de métricas](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer).

1. Na seção Monitoramento, selecione **Métricas** para abrir o explorador de métricas com o escopo definido para o serviço de pesquisa.

1. Em Metric, escolha um da lista de saque e revise a lista de agregações disponíveis para um tipo preferido. A agregação define como os valores coletados serão amostrados ao longo de cada intervalo de tempo.

   ![Explorador de métricas para métrica QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Explorador de métricas para métrica QPS")

1. No canto superior direito, defina o intervalo de tempo.

1. Escolha uma visualização. O padrão é um gráfico de linhas.

1. Camada agregações adicionais escolhendo **Adicionar métrica** e selecionando diferentes agregações.

1. Amplie em uma área de interesse no gráfico de linha. Coloque o ponteiro do mouse no início da área, clique com o botão esquerdo do mouse e mantenha pressionado, arraste para o outro lado da área e solte o botão. O gráfico será ampliado naquele intervalo de tempo.

## <a name="identify-strings-used-in-queries"></a>Identificar strings usadas em consultas

Quando você habilita o registro de diagnóstico, o sistema captura solicitações de consulta na tabela **AzureDiagnostics.** Como pré-requisito, você já deve ter ativado [o registro de diagnóstico,](search-monitor-logs.md)especificando um espaço de trabalho de análise de log ou outra opção de armazenamento.

1. Na seção Monitoramento, selecione **Logs** para abrir uma janela de consulta vazia no Log Analytics.

1. Execute a seguinte expressão para pesquisar Operações de consulta.Pesquisa, retornando um conjunto de resultados tabular que consiste no nome da operação, na seqüência de consultas, no índice consultado e no número de documentos encontrados. As duas últimas instruções excluem as seqüências de consulta que consistem em uma pesquisa vazia ou não especificada, sobre um índice de amostra, o que reduz o ruído em seus resultados.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcionalmente, defina um filtro de coluna no *Query_s* para pesquisar sobre uma sintaxe ou string específica. Por exemplo, você pode filtrar *mais é igual a* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Strings de consulta registrada](./media/search-monitor-usage/log-query-strings.png "Strings de consulta registrada")

Enquanto essa técnica funciona para uma investigação ad hoc, a construção de um relatório permite que você consolide e apresente as strings de consulta em um layout mais propício à análise.

## <a name="identify-long-running-queries"></a>Identifique consultas de longo prazo

Adicione a coluna de duração para obter os números para todas as consultas, não apenas aqueles que são pegos como uma métrica. A classificação destes dados mostra quais consultas levam mais tempo para serem concluídas.

1. Na seção Monitoramento, selecione **Logs** para consultar informações de log.

1. Execute a seguinte consulta para retornar consultas, classificadapor duração em milissegundos. As consultas mais longas estão no topo.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Classificar consultas por duração](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Classificar consultas por duração")

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

Um alerta métrico estabelece um limite no qual você receberá uma notificação ou desencadeará uma ação corretiva que você definirá com antecedência. 

Para um serviço de pesquisa, é comum criar um alerta métrico para latência de pesquisa e consultas estranguladas. Se você souber quando as consultas são retiradas, você pode procurar por remédios que reduzam a carga ou aumentem a capacidade. Por exemplo, se as consultas estranguladas aumentarem durante a indexação, você poderá adiá-la até que a atividade de consulta diminua.

Ao empurrar os limites de uma configuração de partição de réplica específica, configurar alertas para limiares de volume de consulta (QPS) também é útil.

1. Na seção Monitoramento, selecione **Alertas** e clique **em + Nova regra de alerta**. Certifique-se de que seu serviço de pesquisa seja selecionado como recurso.

1. Em Condição, clique em **Adicionar**.

1. Configure a lógica do sinal. Para o tipo de sinal, escolha **métricas** e selecione o sinal.

1. Depois de selecionar o sinal, você pode usar um gráfico para visualizar dados históricos para uma decisão informada sobre como proceder com as condições de configuração.

1. Em seguida, desça até a lógica de alerta. Para a prova de conceito, você pode especificar um valor artificialmente baixo para fins de teste.

   ![Lógica de alerta](./media/search-monitor-usage/alert-logic-qps.png "Lógica de alerta")

1. Em seguida, especifique ou crie um Grupo de Ação. Esta é a resposta para invocar quando o limiar é atingido. Pode ser uma notificação push ou uma resposta automatizada.

1. Por último, especifique detalhes de alerta. Nomeie e descreva o alerta, atribua um valor de gravidade e especifique se criará a regra em um estado habilitado ou desativado.

   ![Detalhes do Alerta](./media/search-monitor-usage/alert-details.png "Detalhes do Alerta")

Se você especificou uma notificação por e-mail, você receberá um e-mail do "Microsoft Azure" com uma linha de assunto "Azure: Gravidade Ativada: 3 `<your rule name>`".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, revise os fundamentos do monitoramento do serviço de busca para saber sobre toda a gama de recursos de supervisão.

> [!div class="nextstepaction"]
> [Monitorar operações e atividades na Pesquisa Cognitiva do Azure](search-monitor-usage.md)