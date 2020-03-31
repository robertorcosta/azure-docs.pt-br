---
title: Leia a entrada em qualquer formato usando desserializadores personalizados .NET no Azure Stream Analytics
description: Este artigo explica o formato de serialização e as interfaces que definem desserializadores personalizados .NET para trabalhos em nuvem e borda do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845267"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leia a entrada em qualquer formato usando desserializadores personalizados .NET

Os desserializadores personalizados .NET permitem que seu trabalho no Azure Stream Analytics leia dados de formatos fora dos três [formatos de dados incorporados](stream-analytics-parsing-json.md). Este artigo explica o formato de serialização e as interfaces que definem desserializadores personalizados .NET para trabalhos em nuvem e borda do Azure Stream Analytics. Há também desserializadores de exemplo para o formato Protocol Buffer e CSV.

## <a name="net-custom-deserializer"></a>Desserializador personalizado .NET

A seguir, as amostras de código são as `StreamDeserializer<T>`interfaces que definem o desserializador personalizado e implementam .

`UserDefinedOperator`é a classe base para todas as operadoras de streaming personalizadas. Ele inicializa `StreamingContext`, que fornece um contexto que inclui mecanismo para publicar diagnósticos para os quais você precisará depurar quaisquer problemas com o seu desserializador.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

O seguinte trecho de código é a desserialização para dados de streaming. 

Erros skippable devem ser `IStreamingDiagnostics` emitidos usando o método Initialize do Pass through. `UserDefinedOperator` Todas as exceções serão tratadas como erros e o desserializador será recriado. Depois de um certo número de erros, o trabalho irá para um status de falha.

`StreamDeserializer<T>`desserializa um fluxo em `T`objeto de tipo . As seguintes condições devem ser atendidas:

1. T é uma classe ou uma estrutura.
1. Todos os campos públicos em T são ou
    1. Um de [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] ou seus equivalentes anulados.
    1. Outra estrutura ou classe seguindo as mesmas regras.
    1. Matriz do `T2` tipo que segue as mesmas regras.
    1. IList`T2` onde T2 segue as mesmas regras.
    1. Não possui nenhum tipo recursivo.

O parâmetro `stream` é o fluxo que contém o objeto serializado. `Deserialize`retorna uma `T` coleção de instâncias.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`fornece contexto que inclui mecanismo para publicação de diagnósticos para o operador de usuário.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`é o diagnóstico para operadores definidos pelo usuário, incluindo serializador, desserializador e funções definidas pelo usuário.

`WriteError`escreve uma mensagem de erro para registros de diagnóstico e envia o erro para diagnósticos.

`briefMessage`é uma breve mensagem de erro. Esta mensagem aparece nos diagnósticos e é usada pela equipe do produto para fins de depuração. Não inclua informações confidenciais e mantenha a mensagem com menos de 200 caracteres

`detailedMessage`é uma mensagem de erro detalhada que só é adicionada aos seus registros de diagnóstico em seu armazenamento. Esta mensagem deve ter menos de 2000 caracteres.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exemplos de desserializador

Esta seção mostra como escrever desserializadores personalizados para Protobuf e CSV. Para exemplos adicionais, como o formato AVRO para Event Hub Capture, visite [o Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato de buffer de protocolo (Protobuf)

Este é um exemplo usando o formato de buffer de protocolo.

Assuma a seguinte definição de buffer de protocolo.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Executando `protoc.exe` a partir do **Google.Protobuf.Tools** NuGet gera um arquivo .cs com a definição. O arquivo gerado não é mostrado aqui.

O seguinte trecho de código é a implementação do desserializador assumindo que o arquivo gerado esteja incluído no projeto. Esta implementação é apenas um invólucro fino sobre o arquivo gerado.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

O seguinte trecho de código é um simples desserializador CSV que também demonstra erros de propagação.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Formato de serialização para APIs REST

Cada entrada do Stream Analytics tem um **formato de serialização**. Para obter mais informações sobre as opções de entrada, consulte a documentação da [API Input REST.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

O seguinte código Javascript é um exemplo do formato de serialização do desserializador .NET ao usar a API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`deve ser uma classe `StreamDeserializer<T>`que implementa . Isso é descrito na seção a seguir.

## <a name="region-support"></a>Suporte de regiões

Este recurso está disponível nas seguintes regiões:

* Centro-Oeste dos EUA
* Norte da Europa
* Leste dos EUA
* Oeste dos EUA
* Leste dos EUA 2
* Europa Ocidental

Você pode [solicitar suporte](https://aka.ms/ccodereqregion) para regiões adicionais.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando esse recurso estará disponível em todas as regiões do Azure?

Este recurso está disponível em [6 regiões](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Se você estiver interessado em usar essa funcionalidade em outra região, você pode [enviar uma solicitação](https://aka.ms/ccodereqregion). O apoio para todas as regiões do Azure está no roteiro.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Posso acessar metadadosPropertyValue a partir de minhas entradas semelhantes à função GetMetadataPropertyValue?

Não há mais suporte para essa funcionalidade. Se você precisar desse recurso, você pode votar para esta solicitação no [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso compartilhar minha implementação dedesserializador com a comunidade para que outros possam se beneficiar?

Uma vez implementado o seu desserializador, você pode ajudar os outros compartilhando-o com a comunidade. Envie seu código para o [repo GitHub do Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Próximas etapas

* [Desserializadores personalizados .NET para trabalhos na nuvem do Azure Stream Analytics](custom-deserializer.md)
