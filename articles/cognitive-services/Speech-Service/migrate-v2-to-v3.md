---
title: Migrar da API REST v2 para v3-serviço de fala
titleSuffix: Azure Cognitive Services
description: Em comparação com a v2, a nova versão da API v3 contém um conjunto de pequenas alterações significativas. Este documento ajudará a migrar para a nova versão principal em nenhum momento.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737955"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migração do v 2.0 para o v 3.0 de fala para API REST de texto

A versão V3 da API REST de fala melhora a versão anterior da API em relação à confiabilidade e à facilidade de uso. O layout da API se alinha mais com o Azure ou API de Serviços Cognitivos. Isso ajuda a aplicar suas habilidades existentes ao usar nossa API de fala.

A visão geral da API está disponível como um [documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Isso é ideal para fornecer uma visão geral da API e testar a nova API.

Estamos fornecendo exemplos para C# e Python. Para transcrições em lote, você encontrará os exemplos no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

## <a name="forward-compatibility"></a>Compatibilidade com encaminhamento

Para garantir uma migração tranqüila para a V3, todas as entidades de v2 também podem ser encontradas na API v3 sob a mesma identidade. Se houver uma alteração de esquema de resultado (por exemplo, transcrições), as respostas para uma versão de obtenção no V3 da API estarão no esquema v3. Se você executar o GET na versão V2 da API, o esquema de resultado estará no formato v2. As entidades recém-criadas no v3 **não estão** disponíveis na v2.

## <a name="breaking-changes"></a>Alterações de quebra

A lista de alterações significativas foi classificada pela magnitude das alterações necessárias para adaptação. Há apenas algumas alterações que exigem uma alteração não trivial no código de chamada. A maioria das alterações requer renomeações simples. O tempo necessário para as equipes migrarem da V2 para a V3 variava entre algumas horas e alguns dias. No entanto, os benefícios de maior estabilidade, código mais simples, respostas mais rápidas deslocam rapidamente o investimento. 

### <a name="host-name-changes"></a>Alterações de nome de host

Os nomes de host foram alterados de {Region}. Cris. ai para {Region}. API. cognitiva. Microsoft. com. Nessa alteração, os caminhos não contêm mais "API/" porque ele faz parte do nome do host. Consulte o [documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) para obter uma descrição completa de regiões e caminhos.

### <a name="identity-of-an-entity"></a>Identidade de uma entidade

A propriedade `id` foi substituída por `self` . Na v2, um usuário de API tinha que saber como nossos caminhos na API estão sendo criados. Isso era não extensível e exigia um trabalho desnecessário do usuário. A propriedade `id` (UUID) é substituída por `self` (cadeia de caracteres), que é o local da entidade (URL). O valor ainda é exclusivo entre todas as suas entidades. Se `id` é armazenado como uma cadeia de caracteres em seu código, uma renomeação simples é suficiente para dar suporte ao novo esquema. Agora você pode usar o `self` conteúdo como URL para todas as suas chamadas REST para sua entidade (Get, patch, Delete).

Se a entidade tiver funcionalidade adicional disponível em outros caminhos, elas serão listadas em `links` . Um bom exemplo é uma transcrição, que tem um método separado para `GET` o conteúdo da transcrição.

transcrição de v2:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

transcrição V3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Dependendo da implementação do cliente, talvez não seja suficiente renomear a propriedade. É recomendável aproveitar o uso dos valores retornados de `self` e `links` como as URLs de destino de suas chamadas REST e não gerar os caminhos em seu cliente. Usando as URLs retornadas, você pode ter certeza de que as alterações futuras em caminhos não quebrarão o código do cliente.

### <a name="working-with-collections-of-entities"></a>Trabalhando com coleções de entidades

Anteriormente, a API v2 retornou todas as entidades disponíveis em uma resposta. Para permitir um controle mais refinado sobre o tamanho de resposta esperado, em v3, todas as respostas de coleções são paginadas. Você tem controle sobre a contagem de entidades retornadas e o deslocamento da página. Esse comportamento facilita a previsão do tempo de execução do processador de resposta e é consistente com outras APIs do Azure.

A forma básica da resposta é a mesma para todas as coleções:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

A propriedade `values` contém um subconjunto das entidades de coleção disponíveis. A contagem e o deslocamento podem ser controlados usando os parâmetros de consulta `skip` e `top` . Quando `@nextLink` não é nulo, há mais dados disponíveis e o próximo lote de dados pode ser recuperado fazendo um get on `$.@nextLink` .

Essa alteração requer chamar o `GET` para a coleção em um loop até que todos os elementos tenham sido retornados.

### <a name="creating-transcriptions"></a>Criando transcrições

Uma descrição detalhada sobre como criar uma transcrição pode ser encontrada em [instruções de transcrição do lote](./batch-transcription.md).

A criação de transcrições foi alterada em V3 para habilitar explicitamente a configuração de opções de transcrição específicas. Todas as propriedades de configuração (opcional) agora podem ser definidas na `properties` propriedade.
A versão v3 também dá suporte a vários arquivos de entrada e, portanto, requer uma lista de URLs e não uma única URL, conforme exigido pela v2. O nome da propriedade foi renomeado de `recordingsUrl` para `contentUrls` . A funcionalidade de análise de sentimentos em transcrições foi removida na v3. É recomendável usar a [análise de texto](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) do serviço cognitiva da Microsoft em vez disso.

A nova propriedade `timeToLive` em `properties` pode ajudar a remover as entidades concluídas existentes. `timeToLive`Especifica uma duração após a qual uma entidade concluída será automaticamente excluída. Defina-o como um valor alto (por exemplo `PT12H` ) quando as entidades são continuamente rastreadas, consumidas e excluídas e, portanto, geralmente processadas antes de 12 horas passadas.

corpo da solicitação de POSTAgem de transcrição v2:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

corpo da solicitação de POSTAgem de transcrição V3:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Formato dos resultados da transcrição v3

O esquema dos resultados da transcrição foi ligeiramente alterado para se alinhar com as transcrições criadas por pontos de extremidade em tempo real. Uma descrição detalhada do novo formato pode ser encontrada no instruções de transcrição do [lote](./batch-transcription.md). O esquema do resultado é publicado em nosso [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) em `samples/batch/transcriptionresult_v3.schema.json` .

Os nomes de propriedade agora são camel-case e os valores de canal e palestrante estão usando tipos inteiros. Para alinhar o formato das durações com outras APIs do Azure, ele agora está formatado conforme descrito em ISO 8601.

Exemplo de um resultado de transcrição v3. As diferenças são descritas nos comentários.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>Obtendo o conteúdo das entidades e os resultados

Na v2, os links para os arquivos de entrada ou resultado foram embutidos com o restante dos metadados de entidade. Como uma melhoria na v3, há uma separação clara entre os metadados de entidade, que é retornada por um GET on `$.self` e os detalhes e as credenciais para acessar os arquivos de resultado. Essa separação ajuda a proteger os dados do cliente e permite um controle fino sobre a duração da validade das credenciais.

No v3, há uma propriedade chamada `files` em links, caso a entidade exponha dados (conjuntos de dados, transcrições, pontos de extremidade, avaliações). Um GET on retornará `$.links.files` uma lista de arquivos e a URL de SAS para acessar o conteúdo de cada arquivo. Para controlar a duração da validade das URLs de SAS, o parâmetro de consulta `sasValidityInSeconds` pode ser usado para especificar o tempo de vida.

transcrição de v2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

transcrição V3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Em seguida, um GET `$.links.files` pode resultar em:

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

O `kind` indica o formato do conteúdo do arquivo. Para transcrições, os arquivos de tipo `TranscriptionReport` são o resumo do trabalho e os arquivos do tipo `Transcription` são o resultado do próprio trabalho.

### <a name="customizing-models"></a>Personalizando modelos

Antes da v3, houve uma distinção entre um "modelo acústico" e um "modelo de linguagem" quando um modelo estava sendo treinado. Essa distinção resultou na necessidade de especificar vários modelos ao criar pontos de extremidade ou transcrições. Para simplificar esse processo para um chamador, removemos as diferenças e fizemos tudo dependente do conteúdo dos conjuntos de valores que estão sendo usados para o treinamento do modelo. Com essa alteração, a criação do modelo agora dá suporte a conjuntos de dados mistos, bem como dados de idioma e acústicos. Os pontos de extremidade e as transcrições agora exigem apenas um modelo.

Com essa alteração, a necessidade de um `kind` na postagem foi removida e `datasets[]` agora o pode conter vários conjuntos de os mesmos ou tipos mistos.

Para melhorar os resultados de um modelo treinado, os dados acústicos são automaticamente usados internamente para o treinamento em idioma. Em geral, os modelos criados por meio da API v3 fornecem resultados mais precisos do que os modelos criados com a API v2.

### <a name="retrieving-base-and-custom-models"></a>Recuperando modelos básicos e personalizados

Para simplificar a obtenção dos modelos disponíveis, a V3 separou as coleções de "modelos básicos" dos "modelos personalizados" de Propriedade do cliente. As duas rotas agora são `GET /speechtotext/v3.0/models/base` e `GET /speechtotext/v3.0/models/` .

Anteriormente, todos os modelos foram retornados juntos em uma única resposta.

### <a name="name-of-an-entity"></a>Nome de uma entidade

O nome da propriedade `name` é renomeado para `displayName` . Isso é alinhado a outras APIs do Azure para não indicar Propriedades de identidade. O valor dessa propriedade não deve ser exclusivo e pode ser alterado após a criação da entidade com um `PATCH` .

transcrição de v2:

```json
{
    "name": "Transcription using locale en-US"
}
```

transcrição V3:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Acessando entidades referenciadas

Em v2, as entidades referenciadas sempre foram embutidas, por exemplo, os modelos usados de um ponto de extremidade. O aninhamento de entidades resultou em grandes respostas e os consumidores raramente consumiram o conteúdo aninhado. Para reduzir o tamanho da resposta e melhorar o desempenho de todos os usuários de API, as entidades referenciadas não são mais embutidas na resposta. Em vez disso, uma referência à outra entidade está sendo usada, que pode ser usada diretamente. Essa referência pode ser usada para um subseqüente `GET` (também é uma URL), seguindo o mesmo padrão que o `self` link.

transcrição de v2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

transcrição V3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Caso você precise consumir os detalhes de um modelo referenciado, conforme mostrado no exemplo acima, simplifique o problema de um GET on `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Recuperando logs de ponto de extremidade

A versão v2 do serviço tem suporte para registrar em log as respostas dos pontos de extremidade. Para recuperar os resultados de um ponto de extremidade com v2, era necessário criar uma "exportação de dados", que representava um instantâneo dos resultados definidos por um intervalo de tempo. O processo de exportação de lotes de dados se tornou inflexível. A API v3 fornece acesso a cada arquivo individual e permite a iteração por meio deles.

Um ponto de extremidade v3 em execução com êxito:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Resposta de GET `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

A paginação para logs de ponto de extremidade funciona de forma semelhante a todas as outras coleções, exceto que nenhum deslocamento pode ser especificado. Devido à grande quantidade de dados disponíveis, a paginação baseada no servidor precisava ser implementada.

No v3, cada log de ponto de extremidade pode ser excluído individualmente emitindo-se uma exclusão no `self` de um arquivo ou usando Delete on `$.links.logs` . Para especificar dados finais, o parâmetro de consulta `endDate` pode ser adicionado à solicitação.

### <a name="using-custom-properties"></a>Usando Propriedades "personalizadas"

Para separar Propriedades personalizadas das propriedades de configuração opcionais, todas as propriedades nomeadas explicitamente agora estão localizadas na `properties` propriedade e todas as propriedades definidas os chamadores agora estão localizados na `customProperties` propriedade.

entidade de transcrição v2

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

entidade de transcrição v3

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Essa alteração também habilitou o uso de tipos corretos em todas as propriedades explicitamente nomeadas em `properties` (por exemplo, bool em vez de cadeia de caracteres).

### <a name="response-headers"></a>Cabeçalhos de resposta

V3 não retorna mais o cabeçalho `Operation-Location` além do cabeçalho `Location` em solicitações post. O valor de ambos os cabeçalhos costumava ser exatamente o mesmo. Agora só `Location` está sendo retornado.

Como a nova versão de API agora é gerenciada pelo gerenciamento de API do Azure (APIM), os cabeçalhos relacionados à limitação `X-RateLimit-Limit` , `X-RateLimit-Remaining` e `X-RateLimit-Reset` não estão contidos nos cabeçalhos de resposta.

### <a name="accuracy-tests"></a>Testes de precisão

Os testes de precisão foram renomeados para avaliações porque o novo nome descreve melhor o que eles representam. Os caminhos de notícias são, por exemplo, "https://{Region}. API. cognitiva. Microsoft. com/speechtotext/v 3.0/reavaliações".
