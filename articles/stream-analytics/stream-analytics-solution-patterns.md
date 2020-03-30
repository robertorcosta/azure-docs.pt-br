---
title: Padrões de solução do Azure Stream Analytics
description: Conheça os padrões comuns de soluções para o Azure Stream Analytics, como dashboarding, mensagens de eventos, armazenamento de dados, enriquecimento de dados de referência e monitoramento.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535775"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões de solução do Azure Stream Analytics

Como muitos outros serviços no Azure, o Stream Analytics é melhor usado com outros serviços para criar uma solução completa maior. Este artigo discute soluções simples do Azure Stream Analytics e vários padrões arquitetônicos. Você pode construir esses padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados em uma grande variedade de cenários. Exemplos de padrões específicos de cenário estão disponíveis nas [arquiteturas de soluções do Azure.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Crie um trabalho de Stream Analytics para alimentar a experiência de dashboarding em tempo real

Com o Azure Stream Analytics, você pode levantar rapidamente os painéis e alertas em tempo real. Uma solução simples ingere eventos do Event Hubs ou do IoT Hub e [alimenta o painel power bi com um conjunto de dados de streaming](/power-bi/service-real-time-streaming). Para obter mais informações, consulte o tutorial detalhado [Analise dados de chamadas telefônicas com stream analytics e visualize os resultados no painel power bi](stream-analytics-manage-job.md).

![Painel ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Esta solução pode ser construída em apenas alguns minutos do portal Azure. Não há codificação extensiva envolvida, e a linguagem SQL é usada para expressar a lógica de negócios.

Este padrão de solução oferece a menor latência da fonte do evento para o painel Power BI em um navegador. O Azure Stream Analytics é o único serviço do Azure com esse recurso integrado.

## <a name="use-sql-for-dashboard"></a>Use SQL para painel

O painel Power BI oferece baixa latência, mas não pode ser usado para produzir relatórios completos de Power BI. Um padrão de relatório comum é levar seus dados para um banco de dados SQL primeiro. Em seguida, use o conector SQL do Power BI para consultar o SQL para obter os dados mais recentes.

![Painel ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

Usar o banco de dados SQL lhe dá mais flexibilidade, mas às custas de uma latência ligeiramente maior. Esta solução é ideal para trabalhos com requisitos de latência maiores que um segundo. Com este método, você pode maximizar os recursos do Power BI para fatiar e dados os dados para relatórios e muito mais opções de visualização. Você também ganha a flexibilidade de usar outras soluções de painel, como o Tableau.

O SQL não é um armazenamento de dados de alto throughput. O throughput máximo para um banco de dados SQL do Azure Stream Analytics está atualmente em torno de 24 MB/s. Se as fontes de evento em sua solução produzirem dados a uma taxa mais alta, você precisará usar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para SQL. Técnicas como filtragem, agregados em janelas, correspondência de padrões com junções temporais e funções analíticas podem ser usadas. A taxa de saída para SQL pode ser otimizada ainda mais usando técnicas descritas na [saída do Azure Stream Analytics para o Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorpore insights em tempo real em seu aplicativo com mensagens de eventos

O segundo uso mais popular do Stream Analytics é gerar alertas em tempo real. Neste padrão de solução, a lógica de negócios no Stream Analytics pode ser usada para detectar padrões ou [anomalias](stream-analytics-machine-learning-anomaly-detection.md) [temporais e espaciais](stream-analytics-geospatial-functions.md) e, em seguida, produzir sinais de alerta. No entanto, ao contrário da solução de painel em que o Stream Analytics usa o Power BI como um ponto final preferido, uma série de sinks de dados intermediários podem ser usados. Essas pias incluem Hubs de Eventos, Ônibus de Serviço e Funções Azure. Você, como construtor de aplicativos, precisa decidir qual o dissipador de dados funciona melhor para o seu cenário.

A lógica do consumidor de eventos a jusante deve ser implementada para gerar alertas no fluxo de trabalho dos negócios existentes. Como você pode implementar a lógica personalizada nas funções do Azure, as funções do Azure são a maneira mais rápida de executar essa integração. Um tutorial para usar a função Azure como a saída para um trabalho do Stream Analytics pode ser encontrado no [Run Azure Functions a partir de trabalhos do Azure Stream Analytics](stream-analytics-with-azure-functions.md). O Azure Functions também suporta vários tipos de notificações, incluindo texto e e-mail. O Logic App também pode ser usado para essa integração, com hubs de eventos entre stream analytics e logic app.

![Aplicativo de mensagens de eventos ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

O Event Hubs, por outro lado, oferece o ponto de integração mais flexível. Muitos outros serviços, como o Azure Data Explorer e o Time Series Insights podem consumir eventos de Hubs de Eventos. Os serviços podem ser conectados diretamente ao dissipador de Hubs de Eventos do Azure Stream Analytics para concluir a solução. Event Hubs também é o corretor de mensagens de throughput mais alto disponível no Azure para tais cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Aplicativos e sites dinâmicos

Você pode criar visualizações personalizadas em tempo real, como visualização de dashboard ou mapa, usando o Azure Stream Analytics e o Azure SignalR Service. Usando o SignalR, os clientes da web podem ser atualizados e mostrar conteúdo dinâmico em tempo real.

![Aplicativo dinâmico ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorpore insights em tempo real em seu aplicativo através de data stores

A maioria dos serviços web e aplicativos web hoje usam um padrão de solicitação/resposta para atender à camada de apresentação. O padrão de solicitação/resposta é simples de ser construído e pode ser facilmente dimensionado com baixo tempo de resposta usando um frontend apátrida e lojas escaláveis, como o Cosmos DB.

O alto volume de dados geralmente cria gargalos de desempenho em um sistema baseado em CRUD. O [padrão de solução de sourcing de eventos](/azure/architecture/patterns/event-sourcing) é usado para lidar com os gargalos de desempenho. Padrões temporais e insights também são difíceis e ineficientes de extrair de um armazenamento de dados tradicional. Os aplicativos modernos baseados em dados de alto volume geralmente adotam uma arquitetura baseada em fluxo de dados. O Azure Stream Analytics como o mecanismo de computação para dados em movimento é um eixo nessa arquitetura.

![Aplicativo de sourcing de eventos ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Neste padrão de solução, os eventos são processados e agregados em armazenamentos de dados pelo Azure Stream Analytics. A camada do aplicativo interage com os armazenamentos de dados usando o padrão tradicional de solicitação/resposta. Devido à capacidade do Stream Analytics de processar um grande número de eventos em tempo real, o aplicativo é altamente escalável sem a necessidade de aumentar a camada do armazenamento de dados. A camada do armazenamento de dados é essencialmente uma visão materializada no sistema. [A saída do Azure Stream Analytics para o Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como o Cosmos DB é usado como uma saída do Stream Analytics.

Em aplicações reais onde a lógica de processamento é complexa e há a necessidade de atualizar certas partes da lógica de forma independente, vários trabalhos de Stream Analytics podem ser compostos em conjunto com o Event Hubs como o intermediário de eventos.

![Aplicativo complexo de sourcing de eventos ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Este padrão melhora a resiliência e a capacidade de gestão do sistema. No entanto, embora o Stream Analytics garanta exatamente uma vez o processamento, há uma pequena chance de que eventos duplicados possam pousar nos Hubs de Eventos intermediários. É importante para o trabalho downstream Stream Analytics dedupe eventos usando chaves lógicas em uma janela de retorno. Para obter mais informações sobre a entrega do evento, consulte a referência [Garantias de Entrega de Eventos.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Use dados de referência para personalização de aplicativos

O recurso de dados de referência do Azure Stream Analytics foi projetado especificamente para personalização do usuário final, como alertar limiares, regras de processamento e [geocercas.](geospatial-scenarios.md) A camada do aplicativo pode aceitar alterações de parâmetros e armazená-las em um banco de dados SQL. O trabalho do Stream Analytics consulta periodicamente as alterações do banco de dados e torna os parâmetros de personalização acessíveis por meio de uma junta de dados de referência. Para obter mais informações sobre como usar dados de referência para personalização de aplicativos, consulte [dados de referência sql](sql-reference-data.md) e [adesão a dados de referência](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Esse padrão também pode ser usado para implementar um mecanismo de regras onde os limites das regras são definidos a partir de dados de referência. Para obter mais informações sobre regras, consulte [Regras baseadas em limiar situadas no Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplicativo de dados de referência ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicione aprendizado de máquina aos seus insights em tempo real

O modelo de detecção de [anomalias](stream-analytics-machine-learning-anomaly-detection.md) incorporada do Azure Stream Analytics é uma maneira conveniente de introduzir o Machine Learning ao seu aplicativo em tempo real. Para obter uma gama mais ampla de necessidades de Machine Learning, consulte [o Azure Stream Analytics integrado ao serviço de pontuação do Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Para usuários avançados que desejam incorporar treinamento on-line e pontuação no mesmo pipeline do Stream Analytics, veja este exemplo de como fazer isso com [regressão linear](stream-analytics-high-frequency-trading.md).

![Aplicativo ASA Machine Learning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Armazenamento de dados em tempo real

Outro padrão comum é o armazenamento de dados em tempo real, também chamado de data warehouse de streaming. Além dos eventos que chegam ao Event Hubs e ao IoT Hub a partir de seu aplicativo, [o Azure Stream Analytics em execução no IoT Edge](stream-analytics-edge.md) pode ser usado para atender a limpeza de dados, redução de dados e necessidades de armazenamento de dados e encaminhamento. O Stream Analytics em execução no IoT Edge pode lidar graciosamente com a limitação da largura de banda e problemas de conectividade no sistema. O adaptador de saída SQL pode ser usado para produzir no SQL Data Warehouse; no entanto, o throughput máximo é limitado a 10 MB/s.

![Armazenamento de dados ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Uma maneira de melhorar o throughput com alguma compensação de latência é arquivar os eventos no armazenamento Azure Blob e, em seguida, [importá-los para o SQL Data Warehouse com polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Você deve costurar manualmente a saída do Stream Analytics para o armazenamento e a entrada do blob storage para o SQL Data [Warehouse, arquivando os dados por carimbo de tempo](stream-analytics-custom-path-patterns-blob-storage-output.md) e importando periodicamente.

Neste padrão de uso, o Azure Stream Analytics é usado como um motor ETL quase em tempo real. Os eventos recém-chegados são continuamente transformados e armazenados para o consumo de serviços de análise a jusante.

![Armazenamento de dados de alto throughput ASA](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arquivamento de dados em tempo real para análise

A maioria das atividades de ciência de dados e análises ainda acontecem offline. Os dados podem ser arquivados pelo Azure Stream Analytics através dos formatos de saída Do Lago DeS1 e do Parquet. Esse recurso remove o atrito para alimentar dados diretamente no Azure Data Lake Analytics, Azure Databricks e No Azure HDInsight. O Azure Stream Analytics é usado como um motor ETL quase em tempo real nesta solução. Você pode explorar dados arquivados no Data Lake usando vários mecanismos de computação.

![Análise off-line ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Use dados de referência para enriquecimento

O enriquecimento de dados é frequentemente um requisito para motores ETL. O Azure Stream Analytics suporta o enriquecimento de dados com dados de [referência](stream-analytics-use-reference-data.md) tanto do banco de dados SQL quanto do armazenamento Azure Blob. O enriquecimento de dados pode ser feito para o desembarque de dados tanto no Azure Data Lake quanto no SQL Data Warehouse.

![Análise off-line asa com enriquecimento de dados](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacionalizar insights a partir de dados arquivados

Se você combinar o padrão de análise offline com o padrão de aplicativo quase em tempo real, você pode criar um loop de feedback. O loop de feedback permite que o aplicativo ajuste automaticamente para mudar padrões nos dados. Esse loop de feedback pode ser tão simples quanto alterar o valor limite para alertar, ou tão complexo quanto retreinar modelos de Machine Learning. A mesma arquitetura de solução pode ser aplicada tanto em trabalhos ASA que são executados na nuvem quanto no IoT Edge.

![Operacionalização de insights asa](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorar os trabalhos da ASA

Um trabalho do Azure Stream Analytics pode ser executado 24 horas por dia, 7 dias por semana, para processar os eventos de entrada continuamente em tempo real. Sua garantia de tempo de atividade é crucial para a saúde da aplicação global. Embora o Stream Analytics seja o único serviço de análise de streaming do setor que oferece uma garantia de disponibilidade de [99,9%,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)você ainda pode incorrer em algum nível de tempo de inatividade. Ao longo dos anos, a Stream Analytics introduziu métricas, registros e estados de trabalho para refletir a saúde dos empregos. Todos eles são fornecidos através do serviço Azure Monitor e podem ser exportados para o OMS. Para obter mais informações, consulte [Understand Stream Analytics job monitoring e como monitorar consultas](stream-analytics-monitoring.md).

![Monitoramento asa](media/stream-analytics-solution-patterns/monitoring.png)

Há duas coisas-chave para monitorar:

- [Emprego estado falido](job-states.md)

    Em primeiro lugar, você precisa ter certeza que o trabalho está funcionando. Sem o trabalho no estado em execução, não são geradas novas métricas ou logs. Os empregos podem mudar para um estado falido por várias razões, incluindo ter um alto nível de utilização de SU (ou seja, ficar sem recursos).

- [Métricas de atraso da marca d'água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Esta métrica reflete o quão longe atrás do seu pipeline de processamento está em tempo de relógio de parede (segundos). Parte do atraso é atribuída à lógica de processamento inerente. Como resultado, monitorar a tendência crescente é muito mais importante do que monitorar o valor absoluto. O atraso do estado constante deve ser abordado pelo projeto do aplicativo, não por monitoramento ou alertas.

Após a falha, registros de atividades e [registros de diagnóstico são](stream-analytics-job-diagnostic-logs.md) os melhores lugares para começar a procurar erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Construa aplicações resilientes e críticas de missão

Independentemente da garantia de SLA do Azure Stream Analytics e do quão cuidadoso você executa seu aplicativo de ponta a ponta, paralisações acontecem. Se sua aplicação é missão crítica, você precisa estar preparado para paralisações a fim de se recuperar graciosamente.

Para alertar aplicativos, o mais importante é detectar o próximo alerta. Você pode optar por reiniciar o trabalho a partir do momento atual ao se recuperar, ignorando alertas passados. A semântica de início do trabalho é pela primeira vez, não pela primeira vez. A entrada é rebobinada para trás uma quantidade apropriada de tempo para garantir que a primeira saída no momento especificado esteja completa e correta. Você não receberá agregados parciais e disparará alertas inesperadamente como resultado.

Você também pode optar por iniciar a saída de algum tempo no passado. Tanto os Hubs de Eventos quanto as políticas de retenção do IoT Hub possuem uma quantidade razoável de dados para permitir o processamento do passado. A compensação é o quão rápido você pode alcançar o tempo atual e começar a gerar novos alertas oportunos. Os dados perdem seu valor rapidamente ao longo do tempo, por isso é importante alcançar o tempo atual rapidamente. Há duas maneiras de recuperar o papo rapidamente:

- Provisão mais recursos (SU) ao recuperar o atraso.
- Reinicie a partir do tempo atual.

Recomeçar da atualidade é simples de fazer, com a troca de deixar uma lacuna durante o processamento. Reiniciar dessa forma pode ser OK para alertar cenários, mas pode ser problemático para cenários de dashboard e não é um não-starter para cenários de arquivamento e armazenamento de dados.

O provisionamento de mais recursos pode acelerar o processo, mas o efeito de ter um aumento da taxa de processamento é complexo.

- Teste que seu trabalho é escalável para um número maior de SUs. Nem todas as consultas são escaláveis. Você precisa ter certeza de que sua consulta está [paralelamente.](stream-analytics-parallelization.md)

- Certifique-se de que há partições suficientes no Upstream Event Hubs ou IoT Hub que você pode adicionar mais TUs (Throughput Units, unidades de throughput) para dimensionar o throughput de entrada. Lembre-se, cada Event Hubs TU maxé rumina a uma taxa de saída de 2 MB/s.

- Certifique-se de ter provisionado recursos suficientes nos dissipadores de saída (ou seja, SQL Database, Cosmos DB), para que eles não acelerem o aumento na saída, o que às vezes pode fazer com que o sistema seja bloqueado.

O mais importante é antecipar a mudança da taxa de processamento, testar esses cenários antes de entrar em produção e estar pronto para dimensionar o processamento corretamente durante o tempo de recuperação de falhas.

No cenário extremo de que todos os eventos de entrada estão todos atrasados, [é possível que todos os eventos atrasados sejam descartados](stream-analytics-time-handling.md) se você tiver aplicado uma janela de chegada tardia ao seu trabalho. A queda dos eventos pode parecer um comportamento misterioso no início; no entanto, considerando que o Stream Analytics é um motor de processamento em tempo real, ele espera que os eventos de entrada estejam perto do tempo do relógio de parede. Tem que abandonar eventos que violam essas restrições.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Arquiteturas ou Processo de Recarga

Felizmente, o padrão anterior de arquivamento de dados pode ser usado para processar esses eventos tardios graciosamente. A ideia é que o trabalho de arquivamento processe eventos de entrada na hora da chegada e arquiva eventos no balde de tempo certo no Azure Blob ou a Azure Data Lake Store com o tempo de evento. Não importa o quão tarde um evento chegue, ele nunca será descartado. Sempre aterrissará no balde da hora certa. Durante a recuperação, é possível reprocessar os eventos arquivados e reabastecer os resultados para a loja escolhida. Isso é semelhante à forma como os padrões lambda são implementados.

![Recarga asa](media/stream-analytics-solution-patterns/backfill.png)

O processo de backfill tem que ser feito com um sistema de processamento de lotes offline, que provavelmente tem um modelo de programação diferente do Azure Stream Analytics. Isso significa que você tem que reimplementar toda a lógica de processamento.

Para o enchimento de back, ainda é importante provisão, pelo menos temporariamente, mais recursos para os dissipadores de saída para lidar com um rendimento maior do que as necessidades de processamento de estado constante.

|Cenários  |Reinicie apenas a partir de agora  |Reinicializá-lo da última vez parada |Reinicie a partir de agora + recarga com eventos arquivados|
|---------|---------|---------|---------|
|**Dashboarding**   |Cria lacuna    |OK para paralisação curta    |Uso para paralisação prolongada |
|**Alertando**   |Aceitável |OK para paralisação curta    |Não é necessário |
|**Aplicativo de sourcing de eventos** |Aceitável |OK para paralisação curta    |Uso para paralisação prolongada |
|**Data warehousing**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise off-line**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntando as peças

Não é difícil imaginar que todos os padrões de solução mencionados acima possam ser combinados em um complexo sistema de ponta a ponta. O sistema combinado pode incluir dashboards, alertas, aplicativo de fornecimento de eventos, armazenamento de dados e recursos de análise off-line.

A chave é projetar seu sistema em padrões composáveis, para que cada subsistema possa ser construído, testado, atualizado e recuperado independentemente.

## <a name="next-steps"></a>Próximas etapas

Agora você viu uma variedade de padrões de soluções usando o Azure Stream Analytics. Em seguida, você pode se aprofundar e criar seu primeiro trabalho do Stream Analytics:

* [Crie um trabalho de Stream Analytics usando o portal Azure](stream-analytics-quick-create-portal.md).
* [Crie um trabalho de Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).
