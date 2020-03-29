---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563287"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Fala-para-texto ou Discurso personalizado para texto

O contêiner fornece APIs de ponto final de consulta baseadas em websocket, que são acessadas através do [Speech SDK](../index.yml). Por padrão, o Speech SDK usa serviços de fala on-line. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o Speech SDK com contêineres, você não precisa fornecer a chave de assinatura de recursos do Azure Speech [ou um token de usuário de autenticação](../rest-speech-to-text.md#authentication).

Confira os exemplos abaixo.

# <a name="c"></a>[C #](#tab/csharp)

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
