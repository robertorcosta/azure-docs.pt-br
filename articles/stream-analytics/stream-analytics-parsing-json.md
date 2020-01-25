---
title: Análise de JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, CSV dados formatados.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cbfa6f8b85814f0f77234e014ade0ff757a4c4b8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720071"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro. Os dados JSON e Avro podem ser estruturados e conter alguns tipos complexos, como objetos aninhados (registros) e matrizes. 




## <a name="record-data-types"></a>Tipos de dados do sistema
Tipos de dados de registro são usados para representar matrizes JSON e Avro quando formatos correspondentes são usados em fluxos de dados de entrada. Esses exemplos demonstram um sensor de exemplo, o que está lendo os eventos de entrada no formato JSON. Aqui está o exemplo de um único evento:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Acessar campos aninhados no esquema conhecido
Use a notação de ponto (.) para acessar facilmente campos aninhados diretamente da sua consulta. Por exemplo, essa consulta seleciona as coordenadas de latitude e longitude sob a propriedade Location nos dados JSON anteriores. A notação de ponto pode ser usada para navegar por vários níveis, conforme mostrado abaixo.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Selecionar todas as propriedades
Você pode selecionar todas as propriedades de um registro aninhado usando o curinga'*'. Considere o exemplo a seguir:

```SQL
SELECT input.Location.*
FROM input
```

O resultado é:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Acessar campos aninhados quando o nome da propriedade for uma variável
Use a função [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) se o nome da propriedade for uma variável. 

Por exemplo, imagine um fluxo de dados de exemplo precisa ser Unido a dados de referência que contenham limites para cada sensor de dispositivo. Um trecho desses dados de referência é mostrado abaixo.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Converter campos de registro em eventos separados
Para converter os campos de registro em eventos separados, use o operador [APLICAR](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics). Por exemplo, se o exemplo anterior tivesse vários registros para SensorReading, a consulta a seguir poderia ser usada para extraí-los em eventos diferentes:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tipos de dados de matrizes

Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores da matriz são detalhadas abaixo. Estes exemplos supõem que os eventos de entrada tem uma propriedade chamada "arrayField" que é um tipo de dados de matriz.

Esses exemplos usam as funções [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)e o operador [Aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

### <a name="working-with-a-specific-array-element"></a>Trabalhando com um elemento de matriz específico
Selecione o elemento da matriz em um índice especificado (selecionando o primeiro elemento da matriz):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Selecionar comprimento da matriz

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Converter elementos de matriz em eventos separados
Selecione todos os elementos de matriz como eventos individuais. O operador [APLICAR](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) interna extrai todos os elementos da matriz como eventos individuais:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Consulte Também
[Tipos de dados no Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
