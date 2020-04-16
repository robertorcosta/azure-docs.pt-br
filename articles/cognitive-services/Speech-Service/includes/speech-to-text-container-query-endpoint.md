---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421899"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Fala-para-texto ou Discurso personalizado para texto

O contêiner fornece APIs de ponto final de consulta baseadas em websocket, que são acessadas através do [Speech SDK](../index.yml). Por padrão, o Speech SDK usa serviços de fala on-line. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o Speech SDK com contêineres, você não precisa fornecer a chave de assinatura de recursos do Azure Speech [ou um token de usuário de autenticação](../rest-speech-to-text.md#authentication).

Confira os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a esta chamada usando o [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)de contêiner:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a esta chamada usando o [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)de contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
