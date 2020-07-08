---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 19c054b72f7aa2ed6108d076a48856919a01462d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838943"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de Fala do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Crie um arquivo index.js e adicione este código.

Preencha os seus valores para `subscriptionKey`, `servcieRegion` e `filename`.

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pullaudiooutputstream"></a>Carregar o arquivo em um PullAudioOutputStream

Para o Node.js, o SDK de Fala não tem compatibilidade nativa com o acesso direto ao arquivo, portanto, vamos abrir o arquivo e gravar nele usando um `PullAudioOutputStream`.


```JavaScript
// create the push stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and push it to the push stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `SpeechSynthesizer`, é preciso criar uma configuração que use a região e a chave de assinatura. Em seguida, insira este código.

> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>Criar uma configuração de Áudio

Agora, é preciso criar um objeto `AudioConfig` que aponte para seu `PullAudioOutputStream`. Insira esse código logo abaixo da configuração de fala.

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pullStream);
```

## <a name="initialize-a-speechsynthesizer"></a>Inicializar um SpeechSynthesizer

Agora, crie o objeto `SpeechSynthesizer` usando os objetos `SpeechConfig` e `AudioConfig` criados anteriormente.

```JavaScript
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>Reconhecer uma frase e exibir os resultados

No objeto `SpeechSynthesizer`, chame o método `speakTextAsync()`. Esse método permite que o serviço de Fala saiba que você está enviando texto para síntese.

Também escreveremos o resultado retornado ou quaisquer erros no console e, finalmente, fecharemos o sintetizador.
```JavaScript
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);
```
## <a name="check-your-code"></a>Verificar o código
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the pull stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and write it to the pull stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
 
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamOutput(pullStream);
  
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);

}());
```
## <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Executar o código usando Node.js
```bash
node index.js
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]