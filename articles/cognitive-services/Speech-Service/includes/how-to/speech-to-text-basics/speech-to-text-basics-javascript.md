---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 04d54afa6a2dc9aab778f927d55b270a1ca2a660
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210793"
---
Um dos principais recursos do serviço de Fala é a capacidade de reconhecer e transcrever a fala humana (frequentemente denominada conversão de fala em texto). Neste guia de início rápido, você aprende a usar o SDK de Fala em seus aplicativos e produtos para executar uma conversão de fala em texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para fazer qualquer coisa, instale o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Dependendo de sua plataforma, use as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador da Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente de destino, use um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Baixe e extraia o arquivo *microsoft.cognitiveservices.speech.sdk.bundle.js* do <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> e coloque-o em uma pasta acessível para o arquivo HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver direcionando a um navegador da Web e usando a tag `<script>`, o prefixo `sdk` não será necessário. O prefixo `sdk` é um alias usado para dar nome ao módulo `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações sobre `import`, confira <a href="https://javascript.info/import-export" target="_blank">exportar e importar <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre `require`, confira <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário?<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true):

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Veja como criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) com a chave e a região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializar um reconhecedor

Depois de criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true), a próxima etapa é inicializar um [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true). Quando inicializa um [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true), você passa seu `speechConfig` para ele. Isso fornece as credenciais necessárias ao serviço de fala para validar sua solicitação.

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

## <a name="recognize-from-microphone-or-file"></a>Reconhecer do microfone ou do arquivo

Se quiser especificar o dispositivo de entrada de áudio, você precisará criar um [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) e passá-lo como um parâmetro ao inicializar o [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true).

Para reconhecer fala usando o microfone de seu dispositivo, crie um `AudioConfig` usando `fromDefaultMicrophoneInput()` e passe a configuração de áudio ao criar o objeto `SpeechRecognizer`.

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Se desejar reconhecer fala de um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `AudioConfig`. No entanto, ao criar o [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true), em vez de chamar `fromDefaultMicrophoneInput()`, você chamará `fromWavFileInput()` e passará o parâmetro `filename`.

> [!IMPORTANT]
> O reconhecimento de fala de um arquivo tem suporte *somente* no SDK do **Node.js**

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Reconhecer fala

A [classe Reconhecedor](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) do SDK de Fala para JavaScript expõe alguns métodos que você pode usar para o reconhecimento de fala.

* Reconhecimento pontual (assíncrono): realiza o reconhecimento em um modo sem bloqueios (assíncrono). Reconhecerá um único enunciado. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado.
* Reconhecimento contínuo (assíncrono): inicia de modo assíncrono a operação de reconhecimento contínuo. O usuário registra-se em eventos e manipula vários estados do aplicativo. Para interromper o reconhecimento contínuo assíncrono, chame [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync).

> [!NOTE]
> Saiba mais sobre como [escolher um modo de reconhecimento de fala](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Reconhecimento pontual

Veja um exemplo de reconhecimento pontual assíncrono com [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync):

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Será necessário escrever código para lidar com o resultado. Este exemplo avalia o [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest&preserve-view=true#reason):

* Imprime o resultado de reconhecimento: `ResultReason.RecognizedSpeech`
* Se não houver correspondência com o reconhecimento, informe o usuário: `ResultReason.NoMatch`
* Se encontrar um erro, imprima a mensagem de erro: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

### <a name="continuous-recognition"></a>Reconhecimento contínuo

O reconhecimento contínuo é um pouco mais complexo que o pontual. É necessário assinar os eventos `Recognizing`, `Recognized` e `Canceled` para obter os resultados do reconhecimento. Para interromper o reconhecimento, chame [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync). Veja um exemplo de como o reconhecimento contínuo é realizado em um arquivo de entrada de áudio.

Comece definindo a entrada e inicializando um [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true):

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Assine os eventos enviados do [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true).

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizing): sinal para os eventos que contêm resultados de reconhecimento intermediários.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognized): sinal para eventos que contêm resultados de reconhecimento finais (indicando uma tentativa de reconhecimento bem-sucedida).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): sinal para eventos que indicam o fim de uma sessão de reconhecimento (operação).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#canceled): sinal para eventos que contêm resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em decorrência de uma solicitação de cancelamento direta ou uma falha de transporte ou protocolo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Com tudo configurado, chame [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modo de Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

Para habilitar o modo de ditado, use o método [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#enabledictation--) no [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum no reconhecimento de fala é especificar o idioma da entrada (ou origem). Veja como você alteraria o idioma de entrada para italiano. No código, localize [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) e adicione esta linha logo abaixo.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A propriedade [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage) espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="improve-recognition-accuracy"></a>Aprimorar a precisão do reconhecimento

Existem algumas maneiras de aprimorar a precisão do reconhecimento com a Fala. Vamos dar uma olhada em listas de frases. As Listas de Frases são usadas para identificar frases conhecidas nos dados do áudio, como o nome de uma pessoa ou um local específico. Palavras ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada em uma lista de frases será usada se uma correspondência exata à frase inteira estiver incluída no áudio. Se uma correspondência exata à frase não for encontrada, o reconhecimento não será assistido.

> [!IMPORTANT]
> O recurso Lista de Frases só está disponível em inglês.

Para usar uma lista de frases, primeiro crie um objeto [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true). Em seguida, adicione palavras e frases específicas com [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true#addphrase-string-).

Todas as alterações em [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) entrarão em vigor no próximo reconhecimento ou após uma reconexão com o serviço de Fala.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se precisar limpar a lista de frases:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para aprimorar a precisão do reconhecimento

As listas de frases são só uma opção para aprimorar a precisão do reconhecimento. Também é possível: 

* [Aprimorar a precisão com a Fala Personalizada](../../../how-to-custom-speech.md)
* [Aprimorar a precisão com modelos de locatário](../../../tutorial-tenant-model.md)
