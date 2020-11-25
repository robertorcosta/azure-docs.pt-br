---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996365"
---
O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio do [SDK de fala](../index.yml). Por padrão, o SDK de fala usa os serviços de fala online. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o SDK de fala com contêineres, você não precisa fornecer a chave de assinatura de recurso de fala do Azure [ou um token de portador de autenticação](../rest-speech-to-text.md#authentication).

Confira os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Para usar essa chamada com o [host](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)do contêiner:

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

Para usar essa chamada com o [ponto de extremidade](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)do contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---