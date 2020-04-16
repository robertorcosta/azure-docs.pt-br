---
title: Referência de API de fala para texto (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a API REST de fala para texto. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400177"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de fala em texto

Como uma alternativa ao [Speech SDK](speech-sdk.md), o serviço Speech permite converter o discurso para o texto usando uma API REST. Cada terminal acessível está associado a uma região. Seu aplicativo requer uma chave de assinatura para o endpoint que você planeja usar. A API REST é muito limitada, e só deve ser usada nos casos em que o [Speech SDK](speech-sdk.md) não puder.

Antes de usar a API REST para texto, entenda:

* As solicitações que usam a API REST e transmitem áudio diretamente só podem conter até 60 segundos de áudio.
* A API REST de fala para texto só retorna os resultados finais. Resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para o seu aplicativo, considere usar o [Speech SDK](speech-sdk.md) ou uma API REST baseada em arquivos, como [transcrição em lote](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e endpoints

O ponto final para a API REST tem esse formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua pelo `<REGION_IDENTIFIER>` identificador que corresponde à região da sua assinatura a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de linguagem deve ser anexado à URL para evitar o recebimento de um erro HTTP 4xx. Por exemplo, o idioma definido para inglês dos EUA usando o ponto de extremidade West US é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a linguagem falada que está sendo reconhecida. Consulte [idiomas suportados](language-support.md#speech-to-text). | Obrigatório |
| `format` | Especifica o formato do resultado. Os valores aceitos são `simple` e `detailed`. Resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. As respostas detalhadas incluem vários resultados com valores de confiança e quatro representações diferentes. A configuração padrão é `simple`. | Opcional |
| `profanity` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `masked`, que substitui `removed`a profanação por asteriscos, que `raw`remove toda a profanação do resultado, ou , que inclui a profanação no resultado. A configuração padrão é `masked`. | Opcional |
| `cid` | Ao usar o [portal Custom Speech](how-to-custom-speech.md) para criar modelos personalizados, você pode usar modelos personalizados através de seu **ID endpoint** encontrado na página **Implantação.** Use o **ID endpoint** como `cid` argumento para o parâmetro de seqüência de consulta. | Opcional |

## <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista cabeçalhos obrigatórios e opcionais para solicitações de fala para texto.

|Cabeçalho| Descrição | Obrigatório/Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura do serviço de Fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |
| `Content-type` | Descreve o formato e o codec dos dados de áudio fornecidos. Os valores aceitos são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obrigatório |
| `Transfer-Encoding` | Especifica que os dados de áudio em partes estão sendo enviados, em vez de um único arquivo. Use este cabeçalho somente se agrupar dados de áudio. | Opcional |
| `Expect` | Se usar transferência em partes, envie `Expect: 100-continue`. O serviço de Fala reconhece a solicitação inicial e aguarda os dados adicionais.| Necessário se enviar dados de áudio em partes. |
| `Accept` | Se fornecido, deve ser `application/json`. O serviço Speech fornece resultados em JSON. Alguns frameworks de solicitação fornecem um valor padrão incompatível. É uma boa prática `Accept`incluir sempre. | Opcional, mas recomendado. |

## <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Bitrate | Taxa de amostragem  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bit  | 16 kHz, mono |
| OGG    | OPUS  | 16-bit  | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da API REST e do WebSocket no serviço Speech. O [Speech SDK](speech-sdk.md) atualmente suporta o formato WAV com codec PCM, bem como [outros formatos](how-to-use-codec-compressed-audio-input-streams.md).

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
| `100` | Continue | A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em pedaços) |
| `200` | OK | A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON. |
| `400` | Solicitação incorreta | Código de idioma não fornecido, não um idioma suportado, arquivo de áudio inválido, etc. |
| `401` | Não Autorizado | Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido. |
| `403` | Proibido | Chave de assinatura ou token de autorização ausente. |

## <a name="chunked-transfer"></a>Transferência em partes

Transferência em`Transfer-Encoding: chunked`pedaços () pode ajudar a reduzir a latência de reconhecimento. Ele permite que o serviço Speech comece a processar o arquivo de áudio enquanto ele é transmitido. A API REST não fornece resultados parciais ou provisórios.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request`é `HttpWebRequest` um objeto conectado ao ponto final REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui esses campos de nível superior.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após capitalização, pontuação, normalização de texto inverso (conversão do texto falado para formas mais curtas, como 200 para "duzentos" ou "Dr. Smith" para "doctor smith"), e mascaramento de palavrões. Apresentar somente em caso de êxito.|
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

O `detailed` formato inclui os mesmos dados `simple` `NBest`do formato, juntamente com , uma lista de interpretações alternativas do mesmo resultado de reconhecimento. Esses resultados são classificados entre os mais prováveis e menos prováveis. A primeira entrada é o mesmo que o resultado do reconhecimento principal.  Ao usar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

Cada objeto no `NBest` lista inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total) |
| `Lexical` | O formato lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas. |
| `MaskedITN` | O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado. |
| `Display` | O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato é definido como `simple`. |

## <a name="sample-responses"></a>Respostas de exemplo

Uma resposta `simple` típica para reconhecimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta `detailed` típica para reconhecimento:

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

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
