---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 1c58d285e77ace20531912ec150a9d44acde61b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399735"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que:

* Você tem uma conta do Azure e uma assinatura do serviço de Fala. Se você não tiver uma conta e uma assinatura – [Experimente o serviço de Fala gratuitamente](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Instalar e importar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala.

```Python
pip install azure-cognitiveservices-speech
```

Se você estiver no macOS e encontrar problemas de instalação, talvez seja necessário executar esse comando primeiro.

```Python
python3 -m pip install --upgrade pip
```

Depois que o SDK de Fala for instalado, importe-o para o seu projeto Python com essa instrução.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Veja como criar um [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) com a chave e a região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Inicializar um reconhecedor

Depois de criar um [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python), a próxima etapa é inicializar um [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python). Ao inicializar um [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python), você precisará passar `speech_config` para ele. Isso fornece as credenciais necessárias ao serviço de fala para validar sua solicitação.

Se você estiver reconhecendo fala por meio do microfone padrão do dispositivo, veja qual deve ser a aparência de [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Se você quiser especificar o dispositivo de entrada de áudio, precisará criar um [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) e fornecer o parâmetro `audio_config` ao inicializar o [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python).

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Se desejar fornecer um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `audio_config`. Contudo, quando você criar um [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python), em vez de fornecer o `device_name`, você usará o parâmetro `filename`.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Reconhecer fala

A [classe Reconhecedor](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) do SDK de Fala do Python expõe alguns métodos que você pode usar para o reconhecimento de fala.

* Reconhecimento pontual (síncrono): realiza o reconhecimento em um modo de bloqueio (síncrono). É retornado depois que um único enunciado é reconhecido. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado. A tarefa retorna o texto de reconhecimento como resultado.
* Reconhecimento pontual (assíncrono): realiza o reconhecimento em um modo sem bloqueios (assíncrono). Reconhecerá um único enunciado. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado.
* Reconhecimento contínuo (síncrono) – inicia de modo síncrono o reconhecimento contínuo. O cliente deve se conectar a `EventSignal` para receber os resultados de reconhecimento. Para interromper o reconhecimento, chame [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Reconhecimento contínuo (assíncrono): inicia de modo assíncrono a operação de reconhecimento contínuo. O usuário precisa se conectar a EventSignal para receber os resultados de reconhecimento. Para interromper o reconhecimento contínuo assíncrono, chame [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Saiba mais sobre como [escolher um modo de reconhecimento de fala](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Reconhecimento pontual

Veja um exemplo de reconhecimento pontual síncrono com [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once):

```Python
result = speech_recognizer.recognize_once()
```

Veja um exemplo de reconhecimento pontual síncrono com [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture):

```Python
result = speech_recognizer.recognize_once_async()
```

Independentemente se você usou o método síncrono ou assíncrono, você precisará escrever algum código para iterar o resultado. Este exemplo avalia o [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python):

* Imprime o resultado de reconhecimento: `speechsdk.ResultReason.RecognizedSpeech`
* Se não houver correspondência com o reconhecimento, informe o usuário: `speechsdk.ResultReason.NoMatch `
* Se encontrar um erro, imprima a mensagem de erro: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Reconhecimento contínuo

O reconhecimento contínuo é um pouco mais complexo que o pontual. Ele requer que você se conecte ao `EventSignal` para receber os resultados de reconhecimento, e para interromper o reconhecimento, você precisa chamar [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) ou [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Veja um exemplo de como o reconhecimento contínuo é realizado em um arquivo de entrada de áudio.

Comece definindo a entrada e inicializando um [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Em seguida, crie uma variável para gerenciar o estado do reconhecimento de fala. Para começar, defina-a como `False`, porque, no início do reconhecimento, podemos pressupor com segurança que ela ainda não está concluída.

```Python
done = False
```

Agora criaremos um retorno de chamada para interromper o reconhecimento contínuo quando um `evt` for recebido. Há algumas coisas que você deve considerar.

* Quando um `evt` é recebido, a mensagem `evt` é impressa.
* Depois que `evt` for recebido, [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) será chamado para interromper o reconhecimento.
* O estado de reconhecimento é alterado para `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Este exemplo de código mostra como conectar retornos de chamada a eventos enviados do [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--).

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): sinal para os eventos que contêm resultados de reconhecimento intermediários.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): sinal para eventos que contêm resultados de reconhecimento finais (indicando uma tentativa de reconhecimento bem-sucedida).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): sinal para eventos que indicam o início de uma sessão de reconhecimento (operação).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): sinal para eventos que indicam o fim de uma sessão de reconhecimento (operação).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): sinal para eventos que contêm resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em decorrência de uma solicitação de cancelamento direta ou uma falha de transporte ou protocolo).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Com tudo configurado, podemos chamar [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Modo de Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

Para habilitar o modo de ditado, use o método [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) no [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum no reconhecimento de fala é especificar o idioma da entrada (ou origem). Veja como você alteraria o idioma de entrada para alemão. No código, localize SpeechConfig e adicione esta linha logo abaixo.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) é um parâmetro que usa uma cadeia de caracteres como argumento. Você pode fornecer qualquer valor na lista de [localidades/idiomas](../../../language-support.md) compatíveis.

## <a name="improve-recognition-accuracy"></a>Aprimorar a precisão do reconhecimento

Existem algumas maneiras de aprimorar a precisão do reconhecimento com o SDK de Fala. Vamos examinar Listas de Frases. As Listas de Frases são usadas para identificar frases conhecidas nos dados do áudio, como o nome de uma pessoa ou um local específico. Palavras ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada em uma lista de frases será usada se uma correspondência exata à frase inteira estiver incluída no áudio. Se uma correspondência exata à frase não for encontrada, o reconhecimento não será assistido.

> [!IMPORTANT]
> O recurso Lista de Frases só está disponível em inglês.

Para usar uma lista de frases, primeiro crie um objeto [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python). Em seguida, adicione palavras e frases específicas com [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

Todas as alterações em [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) entrarão em vigor no próximo reconhecimento ou após uma reconexão com o serviço de Fala.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Se precisar limpar a lista de frases: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para aprimorar a precisão do reconhecimento

As listas de frases são só uma opção para aprimorar a precisão do reconhecimento. Também é possível: 

* [Aprimorar a precisão com a Fala Personalizada](../../../how-to-custom-speech.md)
* [Aprimorar a precisão com modelos de locatário](../../../tutorial-tenant-model.md)