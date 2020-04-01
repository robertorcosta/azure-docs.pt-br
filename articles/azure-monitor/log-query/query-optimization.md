---
title: Otimizar consultas de log no Monitor Azure
description: Práticas recomendadas para otimizar consultas de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411423"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Otimizar consultas de log no Monitor Azure
O Azure Monitor Logs usa [o Azure Data Explorer (ADX)](/azure/data-explorer/) para armazenar dados de log e executar consultas para analisar esses dados. Ele cria, gerencia e mantém os clusters ADX para você e os otimiza para sua carga de trabalho de análise de log. Quando você executa uma consulta, ela é otimizada e roteada para o cluster ADX apropriado que armazena os dados do espaço de trabalho. Tanto o Azure Monitor Logs quanto o Azure Data Explorer usam muitos mecanismos automáticos de otimização de consulta. Embora as otimizações automáticas forneçam um impulso significativo, elas são, em alguns casos, onde você pode melhorar drasticamente o desempenho da consulta. Este artigo explica as considerações de desempenho e várias técnicas para corrigi-las.

A maioria das técnicas são comuns a consultas que são executadas diretamente no Azure Data Explorer e no Azure Monitor Logs, embora existam várias considerações exclusivas do Azure Monitor Logs que são discutidas aqui. Para obter mais dicas de otimização do Azure Data Explorer, consulte [as práticas recomendadas do Consulta](/azure/kusto/query/best-practices).

Consultas otimizadas serão:

- Corra mais rápido, reduza a duração total da execução da consulta.
- Tem menor chance de ser estrangulado ou rejeitado.

Você deve dar atenção especial às consultas que são usadas para uso recorrente e estourado, como dashboards, alertas, Logic Apps e Power BI. O impacto de uma consulta ineficaz nesses casos é substancial.

## <a name="query-performance-pane"></a>Painel de desempenho de consulta
Depois de executar uma consulta no Log Analytics, clique na seta para baixo acima dos resultados da consulta para visualizar o painel de desempenho da consulta que mostra os resultados de vários indicadores de desempenho para a consulta. Esses indicadores de desempenho são descritos na seção a seguir.

![Painel de desempenho de consulta](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicadores de desempenho de consulta

Os seguintes indicadores de desempenho da consulta estão disponíveis para cada consulta executada:

- [CPU total](#total-cpu): Computação geral usada para processar a consulta em todos os nós de computação. Representa o tempo usado para computação, análise e busca de dados. 

- [Dados utilizados para consulta processada :](#data-used-for-processed-query)Dados gerais que foram acessados para processar a consulta. Influenciado pelo tamanho da tabela de destino, tempo utilizado, filtros aplicados e número de colunas referenciadas.

- [Período de tempo da consulta processada :](#time-span-of-the-processed-query)A diferença entre os dados mais novos e os mais antigos que foram acessados para processar a consulta. Influenciado pelo intervalo de tempo explícito especificado para a consulta.

- [Idade dos dados processados](#age-of-processed-data): A lacuna entre agora e os dados mais antigos que foram acessados para processar a consulta. Isso influencia muito a eficiência da busca de dados.

- [Número de espaços de trabalho](#number-of-workspaces): Quantos espaços de trabalho foram acessados durante o processamento da consulta devido à seleção implícita ou explícita.

- [Número de regiões](#number-of-regions): Quantas regiões foram acessadas durante o processamento de consulta baseado devido à seleção implícita ou explícita de espaços de trabalho. Consultas multi-regiões são muito menos eficientes e indicadores de desempenho apresentam cobertura parcial.

- [Paralelismo](#parallelism): Indica o quanto o sistema foi capaz de executar essa consulta em vários nós. Relevante apenas para consultas que tenham alto consumo de CPU. Influenciado pelo uso de funções e operadores específicos.


## <a name="total-cpu"></a>Total CPU
A CPU de computação real que foi investida para processar essa consulta em todos os nós de processamento de consulta. Uma vez que a maioria das consultas são executadas em um grande número de nós, isso geralmente será muito maior do que a duração que a consulta realmente levou para ser executada. 

O tempo de processamento da consulta é gasto em:
- Recuperação de dados – a recuperação de dados antigos consumirá mais tempo do que a recuperação de dados recentes.
- Processamento de dados – lógica e avaliação dos dados. 

Além do tempo gasto nos nós de processamento de consulta, há tempo adicional que é gasto pelo Azure Monitor Logs para: autenticar o usuário e verificar se eles estão autorizados a acessar esses dados, localizar o armazenamento de dados, analisar a consulta e alocar os nós de processamento de consulta. Desta vez, esse tempo não está incluído no tempo total da CPU da consulta.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Filtragem antecipada de registros antes de usar altas funções da CPU

Alguns dos comandos e funções da consulta são pesados no consumo de CPU. Isso é especialmente verdadeiro para comandos que analisam JSON e XML ou extraem expressões regulares complexas. Tal análise pode acontecer explicitamente através [de funções parse_json()](/azure/kusto/query/parsejsonfunction) ou [parse_xml()](/azure/kusto/query/parse-xmlfunction) ou implicitamente ao se referir a colunas dinâmicas.

Essas funções consomem CPU em proporção ao número de linhas que estão processando. A otimização mais eficiente é adicionar onde as condições no início da consulta podem filtrar o máximo de registros possível antes que a função intensiva da CPU seja executada.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é de longe a mais eficiente como a [condição antes](/azure/kusto/query/whereoperator) da análise exclui muitos registros:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Evite usar as cláusulas avaliadas onde as cláusulas

Consultas que contêm [onde](/azure/kusto/query/whereoperator) cláusulas em uma coluna avaliada em vez de em colunas que estão fisicamente presentes no conjunto de dados perdem eficiência. A filtragem em colunas avaliadas impede algumas otimizações do sistema quando grandes conjuntos de dados são manipulados.
Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é mais eficiente do que a [condição](/azure/kusto/query/whereoperator) se refere à coluna incorporada

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Use comandos de agregação eficazes e dimmentions em resumir e participar

Enquanto alguns comandos de agregação como [max()](/azure/kusto/query/max-aggfunction), [soma()](/azure/kusto/query/sum-aggfunction), [contagem()](/azure/kusto/query/count-aggfunction)e [avg()](/azure/kusto/query/avg-aggfunction) têm baixo impacto na CPU devido à sua lógica, outros são mais complexos e incluem heurísticas e estimativas que permitem que eles sejam executados eficientemente. Por exemplo, [dcount()](/azure/kusto/query/dcount-aggfunction) usa o algoritmo HyperLogLog para fornecer uma estimativa próxima a uma contagem distinta de grandes conjuntos de dados sem realmente contar cada valor; as funções percentis estão fazendo aproximações semelhantes usando o algoritmo percentil de classificação mais próximo. Vários dos comandos incluem parâmetros opcionais para reduzir seu impacto. Por exemplo, a função [makeset()](/azure/kusto/query/makeset-aggfunction) tem um parâmetro opcional para definir o tamanho máximo do conjunto, o que afeta significativamente a CPU e a memória.

Os comandos [junte-se](/azure/kusto/query/joinoperator?pivots=azuremonitor) e [sicomandadas](/azure/kusto/query/summarizeoperator) podem causar alta utilização da CPU quando estiverem processando um grande conjunto de dados. Sua complexidade está diretamente relacionada com o número de valores possíveis, referidos `by` como *cardinalidade,* das colunas que estão usando como em resumo ou como atributos de adesão. Para explicação e otimização de participar e resumir, consulte seus artigos de documentação e dicas de otimização.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado porque o **CounterPath** é sempre mapeado para **CounterName** e **ObjectName**. O segundo é mais eficiente, pois a dimensão de agregação é menor:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

O consumo de CPU também pode ser impactado por condições ou colunas estendidas que requerem computação intensiva. Todas as comparações triviais de seqüência, como [equal ==](/azure/kusto/query/datatypes-string-operators) e [startswith](/azure/kusto/query/datatypes-string-operators) têm aproximadamente o mesmo impacto da CPU, enquanto as correspondências de texto avançadas têm mais impacto. Especificamente, o operador [tem](/azure/kusto/query/datatypes-string-operators) é mais eficiente que o operador [contém.](/azure/kusto/query/datatypes-string-operators) Devido às técnicas de manuseio de cordas, é mais eficiente procurar cordas que sejam maiores que quatro caracteres do que cordas curtas.

Por exemplo, as seguintes consultas produzem resultados semelhantes, dependendo da política de nomeação do computador, mas a segunda é mais eficiente:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Este indicador apresenta apenas CPU a partir do cluster imediato. Em consulta multi-regiões, representaria apenas uma das regiões. Na consulta multi-espaço de trabalho, pode não incluir todos os espaços de trabalho.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Evite analisar XML e JSON completos quando o analisador de strings funcionar
A análise completa de um objeto XML ou JSON pode consumir recursos de alta CPU e memória. Em muitos casos, quando apenas um ou dois parâmetros são necessários e os objetos XML ou JSON são simples, é mais fácil analisá-los como strings usando o [operador de análise](/azure/kusto/query/parseoperator) ou [outras técnicas de análise de texto](/azure/azure-monitor/log-query/parse-text). O aumento de desempenho será mais significativo à medida que o número de registros no objeto XML ou JSON aumentar. É essencial quando o número de registros atinge dezenas de milhões.

Por exemplo, a consulta a seguir retornará exatamente os mesmos resultados das consultas acima sem realizar a análise XML completa. Observe que ele faz algumas suposições sobre a estrutura de arquivos XML, como esse elemento FilePath vem depois do FileHash e nenhum deles tem atributos. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Dados usados para consulta processada

Um fator crítico no processamento da consulta é o volume de dados que são digitalizados e usados para o processamento da consulta. O Azure Data Explorer usa otimizações agressivas que reduzem drasticamente o volume de dados em comparação com outras plataformas de dados. Ainda assim, há fatores críticos na consulta que podem impactar o volume de dados que é usado.

No Azure Monitor Logs, a coluna **TimeGenerated** é usada como uma maneira de indexar os dados. Restringir os valores **gerados** pelo tempo para um intervalo o mais estreito possível fará uma melhoria significativa no desempenho da consulta, limitando significativamente a quantidade de dados que devem ser processados.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Evite o uso desnecessário de operadores de pesquisa e sindicato

Outro fator que aumenta os dados que é o processo é o uso de grande número de tabelas. Isso geralmente `search *` acontece `union *` quando e os comandos são usados. Esses comandos forçam o sistema a avaliar e escanear dados de todas as tabelas no espaço de trabalho. Em alguns casos, pode haver centenas de mesas no espaço de trabalho. Tente evitar o máximo possível usando "pesquisa *" ou qualquer pesquisa sem escopo para uma tabela específica.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a última é de longe a mais eficiente:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Adicione filtros iniciais à consulta

Outro método para reduzir o volume de dados é ter [onde](/azure/kusto/query/whereoperator) as condições no início da consulta. A plataforma Azure Data Explorer inclui um cache que permite saber quais partições incluem dados relevantes para uma condição específica. Por exemplo, se uma `where EventID == 4624` consulta contiver, ela distribuiria a consulta apenas para nós que lidam com partições com eventos correspondentes.

As seguintes consultas de exemplo produzem exatamente o mesmo resultado, mas a segunda é mais eficiente:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Reduza o número de colunas recuperadas

Como o Azure Data Explorer é um armazenamento de dados colunar, a recuperação de cada coluna é independente das outras. O número de colunas que são recuperadas influencia diretamente o volume total de dados. Você só deve incluir as colunas na saída necessária [resumindo](/azure/kusto/query/summarizeoperator) os resultados ou [projetando](/azure/kusto/query/projectoperator) as colunas específicas. O Azure Data Explorer tem várias otimizações para reduzir o número de colunas recuperadas. Se ele determinar que uma coluna não é necessária, por exemplo, se ela não for referenciada no comando [resumir,](/azure/kusto/query/summarizeoperator) ela não a recuperará.

Por exemplo, a segunda consulta pode processar três vezes mais dados, pois precisa buscar não uma coluna, mas três:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Período de tempo da consulta processada

Todos os logs no Azure Monitor Logs são particionados de acordo com a coluna **TimeGenerated.** O número de partições que são acessadas está diretamente relacionado com o período de tempo. Reduzir o intervalo de tempo é a maneira mais eficiente de garantir uma execução de consulta rápida.

O intervalo de tempo pode ser definido usando o seletor de intervalo de tempo na tela do Log Analytics, conforme descrito no [escopo de consulta de Log e no intervalo de tempo no Azure Monitor Log Analytics](scope.md#time-range). Este é o método recomendado à medida que o intervalo de tempo selecionado é passado para o backend usando os metadados de consulta. 

Um método alternativo é incluir explicitamente uma [condição em](/azure/kusto/query/whereoperator) **Tempo Gerado** na consulta. Você deve usar este método, pois garante que o período de tempo é fixo, mesmo quando a consulta é usada a partir de uma interface diferente.
Você deve garantir que todas as partes da consulta tenham filtros **TimeGenerated.** Quando uma consulta tem subconsultas buscando dados de várias tabelas ou da mesma tabela, cada uma tem que incluir sua própria [condição.](/azure/kusto/query/whereoperator)

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Certifique-se de que todas as subconsultas tenham filtro TimeGenerated

Por exemplo, na consulta a seguir, enquanto a tabela **Perf** será digitalizada apenas durante o último dia, a tabela **Heartbeat** será digitalizada para toda a sua história, que pode ser de até dois anos:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Um caso comum em que tal erro ocorre é quando [arg_max()](/azure/kusto/query/arg-max-aggfunction) é usado para encontrar a ocorrência mais recente. Por exemplo: 

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Isso pode ser facilmente corrigido adicionando um filtro de tempo na consulta interna:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Outro exemplo para essa falha é ao executar a filtragem do escopo de tempo logo após uma [união](/azure/kusto/query/unionoperator?pivots=azuremonitor) sobre várias tabelas. Ao realizar a união, cada subconsulta deve ser escopo. Você pode usar a declaração [de let](/azure/kusto/query/letstatement) para garantir a consistência do escopo.

Por exemplo, a consulta a seguir irá escanear todos os dados nas *tabelas Heartbeat* e *Perf,* não apenas no último 1 dia:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Esta consulta deve ser corrigida da seguinte forma:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Limitações de medição de tempo

A medição é sempre maior do que o tempo real especificado. Por exemplo, se o filtro na consulta for de 7 dias, o sistema poderá escanear 7,5 ou 8,1 dias. Isso porque o sistema está particionadando os dados em pedaços em tamanho variável. Para garantir que todos os registros relevantes sejam digitalizados, ele verifica toda a partição que pode cobrir várias horas e até mais de um dia.

Há vários casos em que o sistema não pode fornecer uma medição precisa do intervalo de tempo. Isso acontece na maioria dos casos em que a consulta se estende por menos de um dia ou em consultas de vários espaços de trabalho.


> [!IMPORTANT]
> Este indicador apresenta apenas dados processados no cluster imediato. Em consulta multi-regiões, representaria apenas uma das regiões. Na consulta multi-espaço de trabalho, pode não incluir todos os espaços de trabalho.

## <a name="age-of-processed-data"></a>Idade dos dados processados
O Azure Data Explorer usa vários níveis de armazenamento: na memória, discos SSD locais e blobs Azure muito mais lentos. Quanto mais novos os dados, maior é a chance de que ele seja armazenado em um nível mais performático com menor latência, reduzindo a duração da consulta e da CPU. Além dos dados em si, o sistema também tem um cache para metadados. Quanto mais antigos os dados, menor a chance de seus metadados estarem em cache.

Embora algumas consultas exijam o uso de dados antigos, há casos em que dados antigos são usados por engano. Isso acontece quando as consultas são executadas sem fornecer intervalo de tempo em seus meta-dados e nem todas as referências de tabela incluem filtro na coluna **TimeGenerated.** Nesses casos, o sistema irá escaneá-los todos os dados armazenados nessa tabela. Quando a retenção de dados é longa, ela pode cobrir longos intervalos de tempo e, portanto, dados tão antigos quanto o período de retenção de dados.

Esses casos podem ser, por exemplo:

- Não definir o intervalo de tempo no Log Analytics com uma subconsulta que não é limitada. Consulte o exemplo acima.
- Utilização da API sem os parâmetros opcionais do intervalo de tempo.
- Usar um cliente que não force um intervalo de tempo, como o conector Power BI.

Veja exemplos e notas na seção perversa, pois elas também são relevantes neste caso.

## <a name="number-of-regions"></a>Número de regiões
Existem várias situações em que uma única consulta pode ser executada em diferentes regiões:

- Quando vários espaços de trabalho são explicitamente listados, e eles estão localizados em diferentes regiões.
- Quando uma consulta com escopo de recursos está buscando dados e os dados são armazenados em vários espaços de trabalho localizados em diferentes regiões.

A execução de consulta entre regiões requer que o sistema seja serializado e transferido no backend grandes pedaços de dados intermediários que geralmente são muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e utilizar caches.
Se não há nenhuma razão real para escanear todas essas regiões, você deve ajustar o escopo para que ele cubra menos regiões. Se o escopo de recursos for minimizado, mas ainda assim muitas regiões forem usadas, isso pode acontecer devido à má configuração. Por exemplo, registros de auditoria e configurações de diagnóstico são enviados para diferentes espaços de trabalho em diferentes regiões ou há várias configurações de configurações de diagnóstico. 

> [!IMPORTANT]
> Quando uma consulta é executada em várias regiões, as medições de CPU e dados não serão precisas e representarão a medição apenas em uma das regiões.

## <a name="number-of-workspaces"></a>Número de espaços de trabalho
Os espaços de trabalho são recipientes lógicos que são usados para segregar e administrar dados de registros. O backend otimiza as colocações de espaço de trabalho em clusters físicos dentro da região selecionada.

O uso de vários espaços de trabalho pode resultar de: 

- Onde vários espaços de trabalho são explicitamente listados.
- Quando uma consulta com escopo de recursos está buscando dados e os dados são armazenados em vários espaços de trabalho.
 
A execução entre regiões e clusters de consultas requer que o sistema seja serializado e transferido no backend grandes pedaços de dados intermediários que geralmente são muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e utilização de caches.

> [!IMPORTANT]
> Em alguns cenários de espaço de trabalho, as medições de CPU e dados não serão precisas e representarão a medição apenas para poucos dos espaços de trabalho.

## <a name="parallelism"></a>Paralelismo
O Azure Monitor Logs está usando grandes clusters do Azure Data Explorer para executar consultas, e esses clusters variam em escala, potencialmente chegando a dezenas de nós computacionais. O sistema escala automaticamente os clusters de acordo com a lógica e capacidade de colocação do espaço de trabalho.

Para executar uma consulta de forma eficiente, ela é particionada e distribuída para nós de computação com base nos dados necessários para o seu processamento. Há algumas situações em que o sistema não pode fazer isso de forma eficiente. Isso pode levar a uma longa duração da consulta. 

Os comportamentos de consulta que podem reduzir o paralelismo incluem:

- Utilização de funções de serialização e janela, como o [operador serialize](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)e as funções da [linha.](/azure/kusto/query/rowcumsumfunction) Séries tempois e funções de análise do usuário podem ser usadas em alguns desses casos. A serialização ineficiente também pode acontecer se os seguintes operadores não forem usados no final da consulta: [range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    O uso da função de agregação [dcount()](/azure/kusto/query/dcount-aggfunction) força o sistema a ter cópia central dos valores distintos. Quando a escala de dados for alta, considere usar os parâmetros opcionais da função dcount para reduzir a precisão.
-    Em muitos casos, o operador de [adesão](/azure/kusto/query/joinoperator?pivots=azuremonitor) reduz o paralelismo global. Examine a shuffle join como uma alternativa quando o desempenho é problemático.
-    Em consultas de escopo de recursos, as verificações de RBAC pré-execução podem permanecer em situações onde há um número muito grande de atribuições de RBAC. Isso pode levar a verificações mais longas que resultariam em um paralelismo menor. Por exemplo, uma consulta é executada em uma assinatura onde há milhares de recursos e cada recurso tem muitas atribuições de função no nível de recursos, não no grupo de assinatura ou recurso.
-    Se uma consulta estiver processando pequenos pedaços de dados, seu paralelismo será baixo, pois o sistema não irá espalhá-lo em muitos nós computacionais.



## <a name="next-steps"></a>Próximas etapas

- [Documentação de referência para o idioma de consulta kusto](/azure/kusto/query/).
