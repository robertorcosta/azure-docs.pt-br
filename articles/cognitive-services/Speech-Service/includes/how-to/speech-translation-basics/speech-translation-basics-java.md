---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 9f1fdca94edc6d3938c137185e037ba0c57b7a0f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266609"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha uma conta do Azure e assinatura do serviço Speech. Se você não tem uma conta e assinatura, [experimente o serviço Speech gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o Speech SDK. Dependendo da sua plataforma, siga as instruções na seção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo Speech SDK.

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos deste artigo, `import` inclua as seguintes instruções no topo do **. Arquivo* de código Java.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis de ambiente

O exemplo de código-fonte neste artigo depende de variáveis de ambiente para armazenar dados confidenciais, como a chave de assinatura de recursos speech e região. O arquivo de `static final String` código Java contém dois valores atribuídos `SPEECH__SUBSCRIPTION__KEY` a `SPEECH__SERVICE__REGION`partir das variáveis do ambiente das máquinas host, ou seja, e . Ambos os campos estão no escopo da classe, tornando-os acessíveis dentro dos corpos de método da classe. Para obter mais informações sobre variáveis de ambiente, consulte [variáveis de ambiente e configuração do aplicativo](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Crie uma configuração de tradução de fala

Para chamar o serviço de fala usando o Speech [`SpeechTranslationConfig`][config]SDK, você precisa criar um . Esta classe inclui informações sobre sua assinatura, como sua chave e região associada, ponto final, host ou token de autorização.

> [!TIP]
> Independentemente de você estar realizando reconhecimento de fala, síntese de fala, tradução ou reconhecimento de intenções, você sempre criará uma configuração.

Existem algumas maneiras que você [`SpeechTranslationConfig`][config]pode inicializar um:

* Com uma assinatura: passe em uma chave e na região associada.
* Com um ponto final: passe em um ponto final de serviço de fala. Um token de chave ou autorização é opcional.
* Com um host: passe em um endereço de host. Um token de chave ou autorização é opcional.
* Com um token de autorização: passe em um token de autorização e na região associada.

Vamos dar uma olhada em [`SpeechTranslationConfig`][config] como um é criado usando uma chave e região. Consulte a página [de suporte](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) da região para encontrar o identificador da região.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum de tradução de fala é especificar o idioma de entrada (ou fonte). Vamos dar uma olhada em como você mudaria o idioma de entrada para italiano. Em seu código, [`SpeechTranslationConfig`][config] interaja `setSpeechRecognitionLanguage` com a instância, chamando o método.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

A [`setSpeechRecognitionLanguage`][recognitionlang] função espera uma seqüência de formato de idioma. Você pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas](../../../language-support.md)suportados .

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum de tradução de fala é especificar idiomas de tradução de destino, pelo menos um é necessário, mas múltiplos são suportados. No seguinte trecho de código, tanto francês quanto alemão como alvos da língua de tradução.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

A cada [`addTargetLanguage`][addlang]chamada, um novo idioma de tradução de destino é especificado. Em outras palavras, quando a fala é reconhecida a partir do idioma de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config], o próximo passo [`TranslationRecognizer`][recognizer]é inicializar um . Quando você inicializa um [`TranslationRecognizer`][recognizer], você vai `translationConfig`precisar passá-lo o seu . O objeto de configuração fornece as credenciais que o serviço de fala requer para validar sua solicitação.

Se você está reconhecendo a fala usando o microfone padrão [`TranslationRecognizer`][recognizer] do seu dispositivo, aqui está como deve ser:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Se você quiser especificar o dispositivo de entrada de [`AudioConfig`][audioconfig] áudio, `audioConfig` então você precisará [`TranslationRecognizer`][recognizer]criar um e fornecer o parâmetro ao inicializar o seu .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, você fará `AudioConfig` referência ao objeto da seguinte forma:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Se você quiser fornecer um arquivo de áudio em vez de `audioConfig`usar um microfone, você ainda precisará fornecer um . No entanto, [`AudioConfig`][audioconfig]quando você `fromDefaultMicrophoneInput`cria um , `fromWavFileInput` em `filename` vez de ligar , você vai ligar e passar o parâmetro.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Traduzir fala

Para traduzir a fala, o Speech SDK conta com um microfone ou uma entrada de arquivo de áudio. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento e obtenha o resultado.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Para obter mais informações sobre o discurso-para-texto, consulte [os fundamentos do reconhecimento da fala](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetisque traduções

Após um reconhecimento de fala bem-sucedido e tradução, o resultado contém todas as traduções em um dicionário. A [`getTranslations`][translations] função retorna um dicionário com a chave como o idioma de tradução de destino e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e sintetizada em uma língua diferente (fala-a-fala).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe `synthesizing` um evento. O evento é acionado várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado de reconhecimento de tradução. Se você estiver traduzindo para vários idiomas, consulte [a síntese manual](#manual-synthesis). Especifique a voz [`setVoiceName`][voicename] de síntese atribuindo `synthesizing` um e forneça um manipulador de eventos para o evento, obtenha o áudio. O exemplo a seguir salva o áudio traduzido como um arquivo *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos só funciona com uma única tradução, **não** adicione vários idiomas de tradução de destino. Além disso, [`setVoiceName`][voicename] deve ser a mesma língua que a linguagem de tradução alvo, por exemplo; `"de"` poderia mapear `"de-DE-Hedda"`para .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

A [`getTranslations`][translations] função retorna um dicionário que pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar `SpeechSynthesizer` uma `SpeechConfig` instância, o [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] objeto precisa ter sua propriedade definida para a voz desejada. O exemplo a seguir se traduz em cinco idiomas, e cada tradução é então sintetizada para um arquivo de áudio na linguagem neural correspondente.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
