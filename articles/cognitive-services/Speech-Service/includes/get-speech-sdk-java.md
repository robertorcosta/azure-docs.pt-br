---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656705"
---
:::row:::
    :::column span="3":::
        O Java SDK para Android é embalado como um <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android <span class="docon docon-navigate-external x-hidden-focus"> </span>Library) </a>, que inclui as bibliotecas necessárias e as permissões necessárias do Android. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

1. No arquivo *build.gradle* de nível de projeto, adicione o seguinte à `repository` seção:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. No arquivo *build.gradle* de nível de módulo, adicione o seguinte à `dependencies` seção:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Código-fonte java quickstart específico do Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Código-fonte de início rápido do Java Runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>