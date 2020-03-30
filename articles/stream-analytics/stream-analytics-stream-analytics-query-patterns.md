---
title: Padrões de consulta comuns no Azure Stream Analytics
description: Este artigo descreve vários padrões e designs comuns de consulta que são úteis em trabalhos do Azure Stream Analytics.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982308"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Padrões de consulta comuns no Azure Stream Analytics

As consultas no Azure Stream Analytics são expressas em uma linguagem de consulta parecida com o SQL. Essas construções de linguagem estão documentadas no guia [Referência de linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

O design de consulta pode expressar uma lógica de passagem simples para mover dados de eventos de um fluxo de entrada para um armazenamento de dados de saída, ou pode fazer uma rica correspondência de padrões e análise temporal para calcular agregados ao longo de várias janelas de tempo como na [solução Build a IoT usando](stream-analytics-build-an-iot-solution-using-stream-analytics.md) o guia Stream Analytics. Você pode juntar dados de várias entradas para combinar eventos de streaming, e você pode fazer pesquisas contra dados de referência estática para enriquecer os valores do evento. Você também pode gravar dados em várias saídas.

Este artigo descreve soluções para vários padrões comuns de consulta com base em cenários reais.

## <a name="supported-data-formats"></a>Formatos de dados suportados

O Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro.

JSON e Avro podem conter tipos complexos, como matrizes ou objetos aninhados (registros). Para obter mais informações sobre como trabalhar com esses tipos de dados complexos, consulte o artigo [de dados JSON e AVRO de parsing.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Consulta de passagem simples

Uma consulta de passagem simples pode ser usada para copiar os dados do fluxo de entrada na saída. Por exemplo, se um fluxo de dados contendo informações do veículo em tempo real precisar ser salvo em um banco de dados SQL para análise de cartas, uma simples consulta de passagem fará o trabalho.

**Entrada:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Consulta :**

```SQL
SELECT
    *
INTO Output
FROM Input
```

Uma **consulta SELECT** * projeta todos os campos de um evento de entrada e os envia para a saída. Da mesma forma, **select** também pode ser usado para projetar apenas campos necessários a partir da entrada. Neste exemplo, se o veículo *Fazer* e *O Tempo* forem os únicos campos necessários a serem salvos, esses campos podem ser especificados na declaração **SELECT.**

**Entrada:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Saída:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Consulta :**

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Agregação de dados ao longo do tempo

Para calcular informações ao longo de uma janela de tempo, os dados podem ser agregados em conjunto. Neste exemplo, uma contagem é calculada ao longo dos últimos 10 minutos de tempo para cada fabricante de carro específico.

**Entrada:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Saída:**

| Faça | Contagem |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Consulta :**

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Esta agregação agrupa os carros pela *Make* e os conta a cada 10 segundos. A saída tem a *Make* and *Count* de carros que passaram pelo pedágio.

TumblingWindow é uma função de janelas usada para agrupar eventos. Uma agregação pode ser aplicada em todos os eventos agrupados. Para obter mais informações, consulte [funções de janelas](stream-analytics-window-functions.md).

Para obter mais informações sobre agregação, consulte [funções agregadas](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Conversão de dados

Os dados podem ser lançados em tempo real usando o método **CAST.** Por exemplo, o peso do carro pode ser convertido do tipo **nvarchar(max)** para digitar **bigint** e ser usado em um cálculo numérico.

**Entrada:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída:**

| Faça | Peso |
| --- | --- |
| Make1 |3000 |

**Consulta :**

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Use uma declaração **CAST** para especificar seu tipo de dados. Veja a lista de tipos de dados suportados em [tipos de dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Para obter mais informações sobre [funções de conversão de dados](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>String combinando com LIKE e NÃO COMO

**LIKE** e **NOT LIKE** podem ser usados para verificar se um campo corresponde a um determinado padrão. Por exemplo, um filtro pode ser criado para devolver apenas as placas de licença que começam com a letra 'A' e terminam com o número 9.

**Entrada:**

| Faça | License_plate | Hora |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Saída:**

| Faça | License_plate | Hora |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Consulta :**

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Use a declaração **LIKE** para verificar o valor de campo **License_plate.** Ele deve começar com a letra 'A', depois ter qualquer seqüência de zero ou mais caracteres, terminando com o número 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Especifique a lógica para casos/valores diferentes (instruções CASE)

**As** instruções CASE podem fornecer diferentes cálculos para diferentes campos, com base em critérios específicos. Por exemplo, atribua a faixa 'A' aos carros da *Make1* e da faixa 'B' a qualquer outra marca.

**Entrada:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Saída:**

| Faça |Dispatch_to_lane | Hora |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000Z |

**Solução**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

A expressão **CASE** compara uma expressão a um conjunto de expressões simples para determinar seu resultado. Neste exemplo, os veículos da *Make1* são enviados para a faixa 'A' enquanto os veículos de qualquer outra make serão designados para a faixa 'B'.

Para obter mais informações, consulte a [expressão do caso](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Enviar dados para várias saídas

Várias instruções **SELECT** podem ser usadas para produzir dados para diferentes sinks de saída. Por exemplo, um **SELECT** pode produzir um alerta baseado em limiar, enquanto outro pode produzir eventos para o armazenamento blob.

**Entrada:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Saída de arquivamento de saída:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Saída de alerta de saída**:

| Faça | Hora | Contagem |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Consulta :**

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

A cláusula **INTO** informa ao Stream Analytics qual das saídas gravar os dados. O primeiro **SELECT** define uma consulta de passagem que recebe dados da entrada e os envia para a saída chamada **ArchiveOutput**. A segunda consulta faz uma simples agregação e filtragem antes de enviar os resultados para uma saída do sistema de alerta a jusante chamada **AlertOutput**.

Observe que a cláusula **WITH** pode ser usada para definir vários blocos de subconsulta. Esta opção tem o benefício de abrir menos leitores para a fonte de entrada.

**Consulta :**

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Para obter mais informações, consulte a cláusula [ **COM** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Contagem de valores exclusivos

**Count** e **DISTINCT** podem ser usados para contar o número de valores de campo únicos que aparecem no fluxo dentro de uma janela de tempo. Uma consulta pode ser criada para calcular quantos carros exclusivos *passam* pela cabine de pedágio em uma janela de 2 segundos.

**Entrada:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Saída:**

| Count_make | Hora |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Consulta:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** retorna a contagem de valores distintos na coluna **Fazer** dentro de uma janela de tempo.
Para obter mais informações, consulte a função agregado [ **COUNT** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Cálculo sobre eventos passados

A função **LAG** pode ser usada para olhar para eventos passados dentro de uma janela de tempo e compará-los com o evento atual. Por exemplo, a atual fabricante do carro pode ser desviada se for diferente do último carro que passou pelo pedágio.

**Entrada:**

| Faça | Hora |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Saída:**

| Faça | Hora |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Consulta :**

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Use **LAG** para espiar o fluxo de entrada de um evento de volta, recuperando o valor *de Fazer* e comparando-o com o valor de *Fazer* do evento atual e produzir o evento.

Para obter mais informações, consulte [**LAG**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recupere o primeiro evento em uma janela

**IsFirst** pode ser usado para recuperar o primeiro evento em uma janela de tempo. Por exemplo, apagar as informações do primeiro carro a cada intervalo de 10 minutos.

**Entrada:**

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Saída:**

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |

**Consulta :**

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**O IsFirst** também pode particionar os dados e calcular o primeiro evento para cada carro específico *Fazer* encontrado a cada intervalo de 10 minutos.

**Saída:**

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Consulta :**

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Para obter mais informações, consulte [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Retorne o último evento em uma janela

Como os eventos são consumidos pelo sistema em tempo real, não há função que possa determinar se um evento será o último a chegar para essa janela de tempo. Para isso, o fluxo de entrada precisa ser unido a outro onde o tempo de um evento é o tempo máximo para todos os eventos nessa janela.

**Entrada:**

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Saída:**

| License_plate | Faça | Hora |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Consulta :**

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

O primeiro passo na consulta encontra o carimbo de tempo máximo em janelas de 10 minutos, que é o carimbo de hora do último evento para essa janela. A segunda etapa junta os resultados da primeira consulta com o fluxo original para encontrar o evento que corresponda aos últimos carimbos de tempo em cada janela. 

**DATEDIFF** é uma função específica de data que compara e retorna a diferença de tempo entre dois campos DateTime, para obter mais informações, consulte [funções de data](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Para obter mais informações sobre a junção de fluxos, consulte [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Correlacionar eventos em um córrego

Correlacionar eventos no mesmo fluxo pode ser feito olhando para eventos passados usando a função **LAG.** Por exemplo, uma saída pode ser gerada toda vez que dois carros consecutivos do mesmo *Make* passam pelo pedágio nos últimos 90 segundos.

**Entrada:**

| Faça | License_plate | Hora |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Saída:**

| Faça | Hora | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Consulta :**

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

A função **LAG** pode olhar para o fluxo de entrada de um evento de volta e recuperar o valor *Make,* comparando-o com o valor *Make* do evento atual.  Uma vez que a condição é atendida, os dados do evento anterior podem ser projetados usando **LAG** na declaração **SELECT.**

Para obter mais informações, consulte [LAG](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Detectar a duração entre os eventos

A duração de um evento pode ser computada olhando para o último evento Start uma vez que um evento Final é recebido. Essa consulta pode ser útil para determinar o tempo que um usuário passa em uma página ou um recurso.

**Entrada:**  

| Usuário | Recurso | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Saída:**  

| Usuário | Recurso | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Consulta :**

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

A **função LAST** pode ser usada para recuperar o último evento dentro de uma condição específica. Neste exemplo, a condição é um evento de tipo Iniciar, particionando a pesquisa por **PARTIÇÃO por** usuário e recurso. Dessa forma, cada usuário e recurso é tratado de forma independente ao procurar o evento Iniciar. **DURAÇÃO LIMITE** limita a pesquisa de volta no tempo para 1 hora entre os eventos Fim e Início.

## <a name="detect-the-duration-of-a-condition"></a>Detectar a duração de uma condição

Para condições que abrangem vários eventos, a função **LAG** pode ser usada para identificar a duração dessa condição. Por exemplo, suponha que um bug resultou no peso incorreto de todos os carros (acima de 9.000 quilos). Queremos calcular a duração do bug.

**Entrada:**

| Faça | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000Z |2000 |

**Saída:**

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Consulta :**

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
A primeira declaração **SELECT** correlaciona a medição de peso atual com a medição anterior, projetando-a juntamente com a medição atual. O segundo **SELECT** remonta ao último evento onde o *previous_weight* é inferior a 20000, onde o peso atual é menor que 20000 e o *previous_weight* do evento atual foi maior que 20000.

O End_fault é o evento atual não defeituoso onde o evento anterior foi defeituoso, e o Start_fault é o último evento não defeituoso antes disso.

## <a name="periodically-output-values"></a>Valores de saída periodicamente

Em caso de eventos irregulares ou ausentes, uma saída de intervalo regular pode ser gerada a partir de uma entrada de dados mais esparsa. Por exemplo, gere um evento a cada 5 segundos que relatam o ponto de dados visto mais recentemente.

**Entrada:**

| Hora | Valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**(10 primeiras linhas) de saída**:

| Window_end | Last_event. Tempo | Last_event. Valor |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Consulta :**

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Essa consulta gera eventos a cada 5 segundos e gera o último evento recebido anteriormente. A duração **do HOPPINGWINDOW** determina o quanto a consulta parece encontrar o evento mais recente.

Para obter mais informações, consulte [hopping window](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Processar eventos com tempo independente (Subfluxos)

Os eventos podem chegar atrasados ou fora de ordem devido a distorções de relógio entre produtores de eventos, desvios de clock entre partições ou latência de rede.
Por exemplo, o relógio do dispositivo *tollID* 2 está cinco segundos atrás *do TollID* 1, e o relógio do dispositivo para *TollID* 3 está dez segundos atrás *do TollID* 1. Uma computação pode acontecer independentemente para cada pedágio, considerando apenas seus próprios dados de relógio como um carimbo de data.

**Entrada:**

| PlacaDeCarro | Faça | Hora | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**Saída:**

| TollID | Contagem |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Consulta :**

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

A cláusula **TIMESTAMP OVER BY** analisa cada linha do tempo do dispositivo independentemente usando subfluxos. O evento de saída para cada *TollID* é gerado à medida que são computados, o que significa que os eventos são em ordem em relação a cada *TollID* em vez de serem reordenados como se todos os dispositivos estivessem no mesmo relógio.

Para obter mais informações, consulte [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Remover eventos duplicados em uma janela

Ao realizar uma operação, como calcular médias sobre eventos em uma determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo a seguir, o segundo evento é uma duplicata do primeiro.

**Entrada:**  

| deviceId | Hora | Atributo | Valor |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Saída:**  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Consulta :**

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT(DISTINCT Time)** retorna o número de valores distintos na coluna Time dentro de uma janela de tempo. A saída da primeira etapa pode então ser usada para calcular a média por dispositivo, descartando duplicatas.

Para obter mais informações, consulte [COUNT (TEMPO DISTINTO).](/stream-analytics-query/count-azure-stream-analytics)

## <a name="session-windows"></a>Windows de sessão

Uma Janela de Sessão é uma janela que continua se expandindo à medida que os eventos ocorrem e fecha para computação se nenhum evento for recebido após um período específico de tempo ou se a janela atingir sua duração máxima.
Esta janela é particularmente útil ao calcular dados de interação do usuário. Uma janela começa quando um usuário começa a interagir com o sistema e fecha quando não são observados mais eventos, ou seja, o usuário parou de interagir.
Por exemplo, um usuário está interagindo com uma página da Web onde o número de cliques é registrado, uma Janela de Sessão pode ser usada para descobrir quanto tempo o usuário interagiu com o site.

**Entrada:**

| User_id | Hora | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Saída:**

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Consulta :**

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

O **SELECT** projeta os dados relevantes para a interação do usuário, juntamente com a duração da interação. Agrupando os dados por usuário e uma **SessionWindow** que fecha se nenhuma interação acontecer dentro de 1 minuto, com um tamanho máximo de janela de 60 minutos.

Para obter mais informações sobre **sessionWindow,** consulte Session [Window](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Extensibilidade de linguagem com função definida pelo usuário em JavaScript e C #

A linguagem de consulta do Azure Stream Analytics pode ser estendida com funções personalizadas escritas na linguagem JavaScript ou C#. As Funções Definidas pelo Usuário (UDF) são computações personalizadas/complexas que não podem ser facilmente expressas usando a linguagem **SQL.** Esses UDFs podem ser definidos uma vez e usados várias vezes dentro de uma consulta. Por exemplo, um UDF pode ser usado para converter um valor hexadecimal *nvarchar (max)* em um valor *bigint.*

**Entrada:**

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Saída:**

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

A Função Definida pelo Usuário calculará o *valor maior* do HexValue em cada evento consumido.

Para obter mais informações, consulte [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) e [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Correspondência de padrão avançado com MATCH_RECOGNIZE

**MATCH_RECOGNIZE** é um mecanismo avançado de correspondência de padrões que pode ser usado para combinar uma seqüência de eventos a um padrão de expressão regular bem definido.
Por exemplo, um caixa eletrônico está sendo monitorado em tempo real por falhas, durante o funcionamento do caixa eletrônico se houver duas mensagens de aviso consecutivas que o administrador precisa ser notificado.

**Entrada:**

| ATM_id | Operation_id | Return_Code | Hora |
| --- | --- | --- | --- |
| 1 | "Inserindo o pino" | "Success" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Abertura do Slot de Dinheiro" | "Success" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Fechando o slot de dinheiro" | "Success" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Inserindo a quantidade de retirada" | "Success" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Abertura do Slot de Dinheiro" | "Aviso" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Impressão de saldo bancário" | "Aviso" | 2017-01-26T00:10:19.0000000Z |

**Saída:**

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Abertura do Slot de Dinheiro" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Esta consulta corresponde a pelo menos dois eventos de falha consecutivos e gera um alarme quando as condições são atendidas.
**Pattern** define a expressão regular a ser usada na correspondência, neste caso, qualquer número de operações bem sucedidas seguidas por pelo menos duas falhas consecutivas.
O sucesso e a falha são definidos utilizando Return_Code valor e, uma vez cumprida a condição, as **MEDIDAS** são projetadas com *ATM_id*, a primeira operação de aviso e o primeiro tempo de aviso.

Para obter mais informações, consulte [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Consultas geoescésicas e geoespaciais
O Azure Stream Analytics fornece funções geoespaciais incorporadas que podem ser usadas para implementar cenários como gerenciamento de frotas, compartilhamento de carona, carros conectados e rastreamento de ativos.
Os dados geoespaciais podem ser ingeridos em formatos GeoJSON ou WKT como parte do fluxo de eventos ou dados de referência.
Por exemplo, uma empresa especializada na fabricação de máquinas para impressão de passaportes, locação suas máquinas para governos e consulados. A localização dessas máquinas é fortemente controlada para evitar o deslocamento e possível uso para falsificação de passaportes. Cada máquina é equipada com um rastreador GPS, essa informação é retransmitida de volta para um trabalho do Azure Stream Analytics.
A fabricante gostaria de acompanhar a localização dessas máquinas e ser alertada se uma delas deixar uma área autorizada, desta forma eles podem desativar remotamente, alertar as autoridades e recuperar o equipamento.

**Entrada:**

| Equipment_id | Equipment_current_location | Hora |
| --- | --- | --- |
| 1 | "PONTO(-122.132888797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "PONTO(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "PONTO(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "PONTO(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Entrada de dados de referência:**

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.640983866794,-122.13261655434621 47.6409833866794,-122.1326165543462 144061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.64098386794)" |

**Saída:**

| Equipment_id | Equipment_alert_location | Hora |
| --- | --- | --- |
| 1 | "PONTO(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

A consulta permite que o fabricante monitore a localização das máquinas automaticamente, recebendo alertas quando uma máquina deixa a geocerca permitida. A função geoespacial incorporada permite que os usuários usem dados GPS dentro da consulta sem bibliotecas de terceiros.

Para obter mais informações, consulte os cenários de [agregação geofencing e geoespacial com o artigo do Azure Stream Analytics.](geospatial-scenarios.md)

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente [nosso fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
