---
title: Análise de JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, CSV dados formatados.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77484580"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro. Os dados JSON e Avro podem ser estruturados e conter alguns tipos complexos, como objetos aninhados (registros) e matrizes. 

>[!NOTE]
>Os arquivos AVRO criados pela captura do hub de eventos usam um formato específico que exige que você use o recurso de *desserializador personalizado* . Para obter mais informações, consulte [ler entrada em qualquer formato usando desserializadores personalizados do .net](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



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
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

O resultado é:

|DeviceID|Lat|long|Temperatura|Versão|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Selecionar todas as propriedades
Você pode selecionar todas as propriedades de um registro aninhado usando o curinga'*'. Considere o exemplo a seguir:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

O resultado é:

|DeviceID|Lat|long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Acessar campos aninhados quando o nome da propriedade for uma variável

Use a função [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) se o nome da propriedade for uma variável. Isso permite a criação de consultas dinâmicas sem codificar nomes de propriedade.

Por exemplo, imagine que o fluxo de dados de exemplo precisa **ser Unido a dados de referência** contendo limites para cada sensor de dispositivo. Um trecho desses dados de referência é mostrado abaixo.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

A meta aqui é unir nosso conjunto de dados de exemplo da parte superior do artigo a esse dado de referência e gerar um evento para cada medida de sensor acima de seu limite. Isso significa que nosso único evento acima pode gerar vários eventos de saída se vários sensores estiverem acima de seus respectivos limites, graças à junção. Para obter resultados semelhantes sem uma junção, consulte a seção abaixo.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** seleciona a propriedade em *SensorReadings*, que corresponde ao nome da propriedade proveniente dos dados de referência. Em seguida, o valor associado de *SensorReadings* é extraído.

O resultado é:

|DeviceID|Sensorname|AlertMessage|
|-|-|-|
|12345|Umidade|Alerta: sensor acima do limite|

### <a name="convert-record-fields-into-separate-events"></a>Converter campos de registro em eventos separados

Para converter os campos de registro em eventos separados, use o operador [APLICAR](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics).

Com os dados de exemplo originais, a consulta a seguir pode ser usada para extrair propriedades em diferentes eventos.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

O resultado é:

|DeviceID|Sensorname|AlertMessage|
|-|-|-|
|12345|Temperatura|80|
|12345|Umidade|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[objeto Object]|

Usando [with](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics), é possível rotear esses eventos para destinos diferentes:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analisar registro JSON nos dados de referência do SQL
Ao usar o banco de dados SQL do Azure como dado de referência em seu trabalho, é possível ter uma coluna com dados no formato JSON. Um exemplo é mostrado abaixo.

|DeviceID|Dados|
|-|-|
|12345|{"Key": "value1"}|
|54321|{"chave": "value2"}|

Você pode analisar o registro JSON na coluna de *dados* escrevendo uma função simples definida pelo usuário do JavaScript.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Em seguida, você pode criar uma etapa na consulta Stream Analytics, conforme mostrado abaixo, para acessar os campos dos seus registros JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Tipos de dados de matrizes

Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores da matriz são detalhadas abaixo. Esses exemplos usam as funções [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)e o operador [Aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

Aqui está um exemplo de um único evento. Ambos `CustomSensor03` e `SensorMetadata` são do tipo **matriz**:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Trabalhando com um elemento de matriz específico

Selecione o elemento da matriz em um índice especificado (selecionando o primeiro elemento da matriz):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

O resultado é:

|nome do primeiro|
|-|
|12|

### <a name="select-array-length"></a>Selecionar comprimento da matriz

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

O resultado é:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Converter elementos de matriz em eventos separados

Selecione todos os elementos de matriz como eventos individuais. O operador [APLICAR](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) interna extrai todos os elementos da matriz como eventos individuais:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

O resultado é:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
O resultado é:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Fabricante|ABC|
|12345|Versão|1.2.45|

Se os campos extraídos precisarem aparecer em colunas, é possível dinamizar o conjunto de um usando a sintaxe [with](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) , além da operação [Join](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) . Essa junção exigirá uma condição de [limite de tempo](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) que impede a duplicação:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

O resultado é:

|DeviceId|Lat|long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Consulte Também
[Tipos de dados no Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
