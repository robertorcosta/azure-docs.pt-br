---
title: REST (referência de API de fala em texto) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de fala em texto. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ea37dc9ee6c9249aa9d18f7ee7ab1fdbe1230930
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975832"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de fala em texto

Como alternativa ao SDK de [fala](speech-sdk.md), o serviço de fala permite que você converta a fala em texto usando uma API REST. Cada terminal acessível está associado a uma região. Seu aplicativo requer uma chave de assinatura para o endpoint que você planeja usar.

Antes de usar a API REST de fala em texto, entenda:

* As solicitações que usam a API REST e transmitem áudio diretamente podem conter até 60 segundos de áudio.
* A API REST de fala para texto só retorna os resultados finais. Resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para seu aplicativo, considere usar o [SDK de fala](speech-sdk.md) ou uma API REST baseada em arquivo, como a [transcrição do lote](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para transcrição de fala para texto usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| . | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a linguagem falada que está sendo reconhecida. Confira os [Idiomas compatíveis](language-support.md#speech-to-text). | obrigatórios |
| `format` | Especifica o formato do resultado. Os valores aceitos são `simple` e `detailed`. Resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. As respostas detalhadas incluem vários resultados com valores de confiança e quatro representações diferentes. A configuração padrão é `simple`. | Opcional |
| `profanity` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `masked`, que substitui a profanação por asteriscos, `removed`, que remove toda a profanação do resultado, ou `raw`, que inclui a profanação no resultado. A configuração padrão é `masked`. | Opcional |

## <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista cabeçalhos obrigatórios e opcionais para solicitações de fala para texto.

|Cabeçalho| Descrição | Obrigatório/Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura do serviço de Fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |
| `Content-type` | Descreve o formato e o codec dos dados de áudio fornecidos. Os valores aceitos são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | obrigatórios |
| `Transfer-Encoding` | Especifica que os dados de áudio em partes estão sendo enviados, em vez de um único arquivo. Use este cabeçalho somente se agrupar dados de áudio. | Opcional |
| `Expect` | Se usar transferência em partes, envie `Expect: 100-continue`. O serviço de Fala reconhece a solicitação inicial e aguarda os dados adicionais.| Necessário se enviar dados de áudio em partes. |
| `Accept` | Se fornecido, deve ser `application/json`. O serviço de fala fornece resultados em JSON. Algumas estruturas de solicitação fornecem um valor padrão incompatível. É uma boa prática sempre incluir `Accept`. | Opcional, mas recomendado. |

## <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima têm suporte por meio da API REST e do WebSocket no serviço de fala. O [Speech SDK](speech-sdk.md) atualmente dá suporte a apenas o WAV de formato com o codec PCM.

## <a name="sample-request"></a>Solicitação de exemplo

O exemplo abaixo inclui o nome do host e os cabeçalhos necessários. É importante observar que o serviço também espera dados de áudio, o que não está incluído nesta amostra. Como mencionado anteriormente, o chunking é recomendado, no entanto, não é obrigatório.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 100 | Continuar | A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em partes.) |
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON. |
| 400 | Solicitação incorreta | Código de idioma não fornecido, não um idioma com suporte, arquivo de áudio inválido, etc. |
| 401 | Não autorizado | Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido. |
| 403 | Proibido | Chave de assinatura ou token de autorização ausente. |

## <a name="chunked-transfer"></a>Transferência em partes

A transferência em partes (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência de reconhecimento. Ele permite que o serviço de fala comece a processar o arquivo de áudio enquanto ele é transmitido. A API REST não fornece resultados parciais ou provisórios.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request` é um objeto HTTPWebRequest conectado ao ponto de extremidade REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui esses campos de nível superior.

| . | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após a capitalização, a pontuação, a normalização inversa de texto (conversão de texto falado em formas mais curtas, como 200 para "200" ou "Dr. Smith" para "médico Smith") e mascaramento de profanação. Apresentar somente em caso de êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos) no qual a fala reconhecida começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) da fala reconhecida no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Status | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi bem-sucedido e o campo `DisplayText` está presente. |
| `NoMatch` | A fala foi detectada no fluxo de áudio, mas nenhuma palavra do idioma de destino foi combinada. Normalmente, isso significa que o idioma do reconhecimento é um idioma diferente daquele que o usuário está falando. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço atingiu o tempo limite aguardando pela fala. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço atingiu o tempo limite aguardando pela fala. |
| `Error` | O serviço de reconhecimento encontrou um erro interno e não foi possível continuar. Tente novamente, se possível. |

> [!NOTE]
> Se o áudio consistir apenas em conteúdo ofensivo e o parâmetro de consulta `profanity` estiver definido como `remove`, o serviço não retornará um resultado de fala.

O formato de `detailed` inclui os mesmos dados que o formato de `simple`, juntamente com `NBest`, uma lista de interpretações alternativas do mesmo resultado de reconhecimento. Esses resultados são classificados da maior probabilidade de menos provável. A primeira entrada é o mesmo que o resultado do reconhecimento principal.  Ao usar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

Cada objeto no `NBest` lista inclui:

| . | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total) |
| `Lexical` | O formato lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas. |
| `MaskedITN` | O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado. |
| `Display` | O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato é definido como `simple`. |

## <a name="sample-responses"></a>Respostas de exemplo

Uma resposta típica para o reconhecimento de `simple`:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta típica para o reconhecimento de `detailed`:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Próximos passos

- [Obter a assinatura de avaliação do Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
