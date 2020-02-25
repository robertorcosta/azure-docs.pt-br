---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563287"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Conversão de fala em texto ou Fala Personalizada em texto

O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio do [SDK de fala](../index.yml). Por padrão, o SDK de fala usa os serviços de fala online. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o SDK de fala com contêineres, você não precisa fornecer a chave de assinatura de recurso de fala do Azure [ou um token de portador de autenticação](../rest-speech-to-text.md#authentication).

Confira os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para essa chamada usando o [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)do contêiner:

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

para essa chamada usando o [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)do contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
