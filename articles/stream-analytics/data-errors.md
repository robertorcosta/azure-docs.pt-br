---
title: Erros de dados de registro de diagnóstico do Azure Stream Analytics
description: Este artigo explica os diferentes erros de dados de entrada e saída que podem ocorrer ao usar o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398126"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados do Azure Stream Analytics

Erros de dados são erros que ocorrem durante o processamento dos dados.  Esses erros ocorrem mais frequentemente durante a desserialização, serialização e gravação de operações de desserialização de dados.  Quando ocorrem erros de dados, o Stream Analytics grava informações detalhadas e eventos de exemplo nos registros de diagnóstico.  Em alguns casos, o resumo dessas informações também é fornecido através de notificações de portal.

Este artigo descreve os diferentes tipos de erro, causas e detalhes do registro de diagnóstico para erros de dados de entrada e saída.

## <a name="diagnostic-log-schema"></a>Esquema de log de diagnóstico

Consulte [Solucionar problemas do Azure Stream Analytics usando registros de diagnósticopara](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) ver o esquema dos registros de diagnóstico. O JSON a seguir é um exemplo de valor para o campo **Propriedades** de um registro de diagnóstico para um erro de dados.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Erros de dados de entrada

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Causa: O tipo de compactação de entrada selecionado não corresponde aos dados.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: As mensagens com quaisquer erros de desserialização, incluindo o tipo de compactação inválida, são retiradas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. Para o Event Hub, o identificador é o PartitionId, Offset e Sequence Number.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: O cabeçalho dos dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: As mensagens com quaisquer erros de desserialização, incluindo cabeçalho inválido, são retiradas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: As colunas de entrada definidas com CREATE TABLE ou através do TIMESTAMP BY não existem.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Eventos com colunas ausentes são retirados da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Nomes das colunas que estão faltando. 
   * Carga real até alguns kilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Não é possível converter a entrada para o tipo especificado na declaração TABELA CRIAR.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Eventos com erro de conversão de tipo são retirados da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Nome da coluna e tipo esperado.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Causa: Os dados de entrada não estão no formato certo. Por exemplo, a entrada não é JSON válida.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os eventos na mensagem após um erro de dados inválido sumido são retirados da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real até poucos kilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Causa: O valor da expressão TIMESTAMP BY não pode ser convertido em data-hora.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Eventos com carimbo de entrada inválido são retirados da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Mensagem de erro. 
   * Carga real até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: O valor do CARIMBO DE TEMPO POR OVER TimestampColumn é NULO.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Eventos com chave de carimbo de tempo de entrada inválida são retirados da entrada.
* Detalhes do log
   * A carga real até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>Evento de entrada tardia

* Causa: A diferença entre o tempo de aplicação e o tempo de chegada é maior do que a janela de tolerância de chegada tardia.
* Notificação do portal fornecida: Não
* Nível de registro de diagnóstico: Informações
* Impacto: Os eventos de entrada tardia são tratados de acordo com a configuração "Lidar com outros eventos" na seção Deordem de eventos da configuração do trabalho. Para obter mais informações, consulte [Políticas de manipulação de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora da aplicação e tempo de chegada. 
   * Carga real até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Causa: A diferença entre o tempo de aplicação e o tempo de chegada é superior a 5 minutos.
* Notificação do portal fornecida: Não
* Nível de registro de diagnóstico: Informações
* Impacto: Os eventos de entrada antecipada são tratados de acordo com a configuração "Lidar com outros eventos" na seção Deordem de eventos da configuração do trabalho. Para obter mais informações, consulte [Políticas de manipulação de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora da aplicação e tempo de chegada. 
   * Carga real até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>Evento de soforder

* Causa: O evento é considerado fora de ordem de acordo com a janela de tolerância fora da ordem definida.
* Notificação do portal fornecida: Não
* Nível de registro de diagnóstico: Informações
* Impacto: Eventos fora de ordem são tratados de acordo com a configuração "Lidar com outros eventos" na seção Deordem de eventos da configuração do trabalho. Para obter mais informações, consulte [Políticas de manipulação de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Carga real até poucos kilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de dados de saída

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.requiredColunaausente

* Causa: A coluna necessária para a saída não existe. Por exemplo, uma coluna definida como Azure Table PartitionKey não existe.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a coluna necessária ausente, são tratados de acordo com a configuração [Política de dados de](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) saída.
* Detalhes do log
   * Nome da coluna e identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>SaídaDataErroconversãodeerro.ColunaNomeInvalid

* Causa: O valor da coluna não está de acordo com a saída. Por exemplo, o nome da coluna não é uma coluna de tabela azure válida.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o nome da coluna inválida, são tratados de acordo com a configuração [Política de dados de](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) saída.
* Detalhes do log
   * Nome da coluna e identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: Uma coluna não pode ser convertida em um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com restrições ou tipo definido na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo erro de conversão de tipo, são tratados de acordo com a configuração [Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do log
   * Nome da coluna.
   * Ou identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: O valor da mensagem é maior do que o tamanho de saída suportado. Por exemplo, um registro é maior que 1 MB para uma saída do Event Hub.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o limite de tamanho superior ao registro, são tratados de acordo com a configuração [Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do log
   * Ou identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Um registro já contém uma coluna com o mesmo nome de uma coluna do Sistema. Por exemplo, a saída do CosmosDB com uma coluna chamada ID quando a coluna ID é para uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de registro de diagnóstico: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a tecla duplicada, são tratados de acordo com a configuração [Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do log
   * Nome da coluna.
   * Ou identificador de registro ou parte do registro.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas do Stream Analytics do Azure usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Noções básicas sobre o monitoramento de trabalhos do Stream Analytics e como monitorar consultas](stream-analytics-monitoring.md)
