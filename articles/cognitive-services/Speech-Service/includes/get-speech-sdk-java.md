---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637561"
---
:::row:::
    :::column span="3":::
        O SDK do Java para Android é empacotado como um <a href="https://developer.android.com/studio/projects/android-library" target="_blank">aar (biblioteca do <span class="docon docon-navigate-external x-hidden-focus"></span> Android) </a>, que inclui as bibliotecas necessárias e as permissões do Android necessárias. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

1. No arquivo *Build. gradle* de nível de projeto, adicione o seguinte à `repository` seção:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. No arquivo *Build. gradle* de nível de módulo, adicione o seguinte à `dependencies` seção:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Código-fonte do início rápido de Java específico do Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Código-fonte de início rápido do JRE (Java Runtime)<span class="docon docon-navigate-external x-hidden-focus"></span></a>