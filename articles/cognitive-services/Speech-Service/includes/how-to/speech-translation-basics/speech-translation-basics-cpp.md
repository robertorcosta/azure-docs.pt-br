---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 7c57952d0b78271bbcc45bd282385524772f0f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266642"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha uma conta do Azure e assinatura do serviço Speech. Se você não tem uma conta e assinatura, [experimente o serviço Speech gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o Speech SDK. Dependendo da sua plataforma, siga as instruções na seção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo Speech SDK.

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos deste artigo, `#include` `using` inclua as seguintes e instruções na parte superior do arquivo de código C++.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis de ambiente

O exemplo de código-fonte neste artigo depende de variáveis de ambiente para armazenar dados confidenciais, como a chave de assinatura de recursos speech e região. O arquivo de código C++ contém dois valores de seqüência que são atribuídos a partir das variáveis do ambiente das máquinas host, ou seja, `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION`. Ambos os campos estão no escopo da classe, tornando-os acessíveis dentro dos corpos de método da classe. Para obter mais informações sobre variáveis de ambiente, consulte [variáveis de ambiente e configuração do aplicativo](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum de tradução de fala é especificar o idioma de entrada (ou fonte). Vamos dar uma olhada em como você mudaria o idioma de entrada para italiano. Em seu código, [`SpeechTranslationConfig`][config] interaja `SetSpeechRecognitionLanguage` com a instância, chamando o método.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] propriedade espera uma seqüência de formato de idioma local. Você pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas](../../../language-support.md)suportados .

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum de tradução de fala é especificar idiomas de tradução de destino, pelo menos um é necessário, mas múltiplos são suportados. No seguinte trecho de código, tanto francês quanto alemão como alvos da língua de tradução.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

A cada [`AddTargetLanguage`][addlang]chamada, um novo idioma de tradução de destino é especificado. Em outras palavras, quando a fala é reconhecida a partir do idioma de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config], o próximo passo [`TranslationRecognizer`][recognizer]é inicializar um . Quando você inicializa um [`TranslationRecognizer`][recognizer], você vai `translationConfig`precisar passá-lo o seu . O objeto de configuração fornece as credenciais que o serviço de fala requer para validar sua solicitação.

Se você está reconhecendo a fala usando o microfone padrão [`TranslationRecognizer`][recognizer] do seu dispositivo, aqui está como deve ser:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Se você quiser especificar o dispositivo de entrada de [`AudioConfig`][audioconfig] áudio, `audioConfig` então você precisará [`TranslationRecognizer`][recognizer]criar um e fornecer o parâmetro ao inicializar o seu .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, você fará `AudioConfig` referência ao objeto da seguinte forma:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Se você quiser fornecer um arquivo de áudio em vez de `audioConfig`usar um microfone, você ainda precisará fornecer um . No entanto, [`AudioConfig`][audioconfig]quando você `FromDefaultMicrophoneInput`cria um , `FromWavFileInput` em `filename` vez de ligar , você vai ligar e passar o parâmetro.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzir fala

Para traduzir a fala, o Speech SDK conta com um microfone ou uma entrada de arquivo de áudio. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento e obtenha o resultado.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Para obter mais informações sobre o discurso-para-texto, consulte [os fundamentos do reconhecimento da fala](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetisque traduções

Após um reconhecimento de fala bem-sucedido e tradução, o resultado contém todas as traduções em um dicionário. A [`Translations`][translations] chave do dicionário é o idioma de tradução de destino e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e sintetizada em uma língua diferente (fala-a-fala).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe `Synthesizing` um evento. O evento é acionado várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado de reconhecimento de tradução. Se você estiver traduzindo para vários idiomas, consulte [a síntese manual](#manual-synthesis). Especifique a voz [`SetVoiceName`][voicename] de síntese atribuindo `Synthesizing` um e forneça um manipulador de eventos para o evento, obtenha o áudio. O exemplo a seguir salva o áudio traduzido como um arquivo *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos só funciona com uma única tradução, **não** adicione vários idiomas de tradução de destino. Além disso, [`SetVoiceName`][voicename] deve ser a mesma língua que a linguagem de tradução alvo, por exemplo; `"de"` poderia mapear `"de-DE-Hedda"`para .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

O [`Translations`][translations] dicionário pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar `SpeechSynthesizer` uma `SpeechConfig` instância, o [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] objeto precisa ter sua propriedade definida para a voz desejada. O exemplo a seguir se traduz em cinco idiomas, e cada tradução é então sintetizada para um arquivo de áudio na linguagem neural correspondente.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
