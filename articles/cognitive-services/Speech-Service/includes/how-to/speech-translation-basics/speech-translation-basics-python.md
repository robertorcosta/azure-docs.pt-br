---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399676"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo da sua plataforma, siga as instruções na seção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo Speech SDK.

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos deste artigo, `import` inclua as seguintes instruções na parte superior do arquivo de código python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis de ambiente

O exemplo de código-fonte neste artigo depende de variáveis de ambiente para armazenar dados confidenciais, como a chave de assinatura de recursos speech e região. O arquivo de código python contém dois valores atribuídos `SPEECH__SUBSCRIPTION__KEY` das `SPEECH__SERVICE__REGION`variáveis do ambiente das máquinas host, ou seja, e . Ambas as variáveis estão no escopo global, tornando-as acessíveis dentro da definição de função do arquivo de código. Para obter mais informações sobre variáveis de ambiente, consulte [variáveis de ambiente e configuração do aplicativo](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Crie uma configuração de tradução de fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechTranslationConfig`][config]. Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!TIP]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechTranslationConfig`][config]:

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Veja como criar um [`SpeechTranslationConfig`][config] com a chave e a região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum de tradução de fala é especificar o idioma de entrada (ou fonte). Veja como você alteraria o idioma de entrada para italiano. Em seu código, [`SpeechTranslationConfig`][config] interaja com `speech_recognition_language` a instância, atribuindo à propriedade.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

A propriedade [`speech_recognition_language`][recognitionlang] espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum de tradução de fala é especificar idiomas de tradução de destino, pelo menos um é necessário, mas múltiplos são suportados. No seguinte trecho de código, tanto francês quanto alemão como alvos da língua de tradução.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

A cada [`add_target_language`][addlang]chamada, um novo idioma de tradução de destino é especificado. Em outras palavras, quando a fala é reconhecida a partir do idioma de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config], a próxima etapa é inicializar um [`TranslationRecognizer`][recognizer]. Ao inicializar um [`TranslationRecognizer`][recognizer], você precisará passar `translation_config` para ele. O objeto de configuração fornece as credenciais que o serviço de fala requer para validar sua solicitação.

Se você estiver reconhecendo fala por meio do microfone padrão do dispositivo, veja qual deve ser a aparência de [`TranslationRecognizer`][recognizer]:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Se você quiser especificar o dispositivo de entrada de áudio, precisará criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro `audio_config` ao inicializar o [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, você fará `AudioConfig` referência ao objeto da seguinte forma:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Se desejar fornecer um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `audioConfig`. No entanto, [`AudioConfig`][audioconfig]quando você cria `use_default_microphone=True`um , em `filename="path-to-file.wav"` vez `filename` de ligar com , você vai ligar e fornecer o parâmetro.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Traduzir fala

Para traduzir a fala, o Speech SDK conta com um microfone ou uma entrada de arquivo de áudio. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento e obtenha o resultado.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Para obter mais informações sobre o discurso-para-texto, consulte [os fundamentos do reconhecimento da fala](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetisque traduções

Após um reconhecimento de fala bem-sucedido e tradução, o resultado contém todas as traduções em um dicionário. A [`translations`][translations] chave do dicionário é o idioma de tradução de destino e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e sintetizada em uma língua diferente (fala-a-fala).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe `Synthesizing` um evento. O evento é acionado várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado de reconhecimento de tradução. Se você estiver traduzindo para vários idiomas, consulte [a síntese manual](#manual-synthesis). Especifique a voz [`voice_name`][voicename] de síntese atribuindo `Synthesizing` um e forneça um manipulador de eventos para o evento, obtenha o áudio. O exemplo a seguir salva o áudio traduzido como um arquivo *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos só funciona com uma única tradução, **não** adicione vários idiomas de tradução de destino. Além disso, [`voice_name`][voicename] deve ser a mesma língua que a linguagem de tradução alvo, por exemplo; `"de"` poderia mapear `"de-DE-Hedda"`para .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Síntese manual

O [`translations`][translations] dicionário pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar `SpeechSynthesizer` uma `SpeechConfig` instância, o [`speech_synthesis_voice_name`][speechsynthesisvoicename] objeto precisa ter sua propriedade definida para a voz desejada. O exemplo a seguir se traduz em cinco idiomas, e cada tradução é então sintetizada para um arquivo de áudio na linguagem neural correspondente.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
