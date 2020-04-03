---
title: Notas de lançamento - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Um registro de log de versões do Serviço de Fala, melhorias, correções de bugs e problemas conhecidos.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607414"
---
# <a name="release-notes"></a>Notas de versão
## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: Lançamento 2020-Março

**Novos recursos**

- Linux: Adicionado suporte para Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 com [instruções](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) sobre como configurar o sistema para Speech SDK.
- Linux: Adicionado suporte para .NET Core C# no Linux ARM32 e ARM64. Leia mais [aqui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: `UtteranceId` `ConversationTranscriptionResult`Adicionado em , um ID consistente em todos os intermediários e resultado final de reconhecimento de fala. Detalhes para [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Suporte `Language ID`adicionado para . Consulte speech_sample.py no [repo do GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: Adicionado suporte ao formato de entrada de áudio compactado na plataforma Windows para todos os aplicativos do console win32. Detalhes [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams) 
- JavaScript: Suporte a síntese de fala (texto-para-fala) no NodeJS. Saiba mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Adicione novas API's para permitir a inspeção de todas as mensagens enviadas e recebidas. Saiba mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Correções**

- C#, C++: Corrigido um `SendMessageAsync` problema e agora envia mensagem binária como tipo binário. Detalhes para [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Corrigimos `Connection MessageReceived` um problema no `Recognizer` qual o `Connection` uso do evento pode causar falha se for descartado antes do objeto. Detalhes para [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: O tamanho do buffer de áudio do microfone diminuiu de 800ms para 100ms para melhorar a latência.
- Android: Corrigido um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) com o emulador Android x86 no Android Studio.
- JavaScript: Adicionado suporte para regiões `fromSubscription` na China com a API. Detalhes [aqui.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-) 
- JavaScript: Adicione mais informações de erro para falhas de conexão do NodeJS.
        
**Exemplos**

- Unidade: O reconhecimento da intenção é fixo, onde a importação de LUIS json estava falhando. Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: Amostra `Language ID`adicionada para . Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Teste abreviado de Covid19**

Devido ao trabalho remoto nas últimas semanas, não pudemos fazer tanto teste manual de verificação de dispositivos como normalmente fazemos. Um exemplo disso é testar a entrada de microfone e a saída do alto-falante no Linux, iOS e macOS. Não fizemos nenhuma mudança que achamos que poderia ter quebrado qualquer coisa nessas plataformas, e nossos testes automatizados todos passaram. No caso improvável de perdermos algo, por favor, avise-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen).<br> Obrigado por seu apoio contínuo. Como sempre, por favor, poste perguntas ou comentários no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) ou [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Mantenha-se saudável!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: lançamento 2020-fevereiro

**Novos recursos**

 - Adicionadopacotes Python para suportar a nova versão 3.8 do Python.
 - Suporte ao Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Os clientes devem configurar o OpenSSL de acordo com [estas instruções](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Suporte ao Linux ARM32 para Debian e Ubuntu.
 - O DialogServiceConnector agora suporta um parâmetro opcional de "Bot ID" no BotFrameworkConfig. Este parâmetro permite o uso de vários bots direct line speech com um único recurso de fala Do Zure. Sem o parâmetro especificado, o bot padrão (conforme determinado pela página de configuração do canal Direct Line Speech) será usado.
 - O DialogServiceConnector agora tem uma propriedade SpeechActivityTemplate. O conteúdo desta seqüência JSON será usado pelo Direct Line Speech para pré-povoar uma ampla variedade de campos suportados em todas as atividades que atingem um bot Direct Line Speech, incluindo atividades geradas automaticamente em resposta a eventos como reconhecimento de voz.
 - O TTS agora usa a chave de assinatura para autenticação, reduzindo a latência do primeiro byte do primeiro resultado de síntese depois de criar um sintetizador.
 - Modelos de reconhecimento de fala atualizados para 19 localidades para uma redução média da taxa de erro de palavras de 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Os novos modelos trazem melhorias significativas em vários domínios, incluindo cenários de Ditado, Transcrição de Call-Center e Indexação de Vídeo.

**Correções**

 - Corrigido bug onde Transcriber de Conversação não esperou adequadamente em APIs JAVA 
 - Correção do emulador Android x86 para problema do [Xamarin GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Adicionar falta (Get| Definir)Métodos de propriedade para AudioConfig
 - Corrija um bug TTS onde o audioDataStream não pôde ser interrompido quando a conexão falhar
 - Usar um ponto final sem uma região causaria falhas da USP para o tradutor de conversação
 - A geração de ID em Aplicativos Universais do Windows agora usa um algoritmo GUID apropriadamente exclusivo; anteriormente e involuntariamente padrão para uma implementação stubbed que muitas vezes produzia colisões sobre grandes conjuntos de interações.
 
 **Exemplos**
 
 - Amostra de unidade para usar O SDK de fala com [microfone Unity e fluxo de modo push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Outras alterações**

 - [Documentação de configuração openSSL atualizada para Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: lançamento 2020-janeiro

**Novos recursos**

- Conversa com vários dispositivos: conecte vários dispositivos à mesma conversa baseada em fala ou texto e, opcionalmente, traduza mensagens enviadas entre eles. Saiba mais [neste artigo](multi-device-conversation.md). 
- Suporte a reconhecimento de palavras-chave adicionado para o pacote Android .aar e adicionado suporte para sabores x86 e x64. 
- Objetivo-C: `SendMessage` `SetMessageProperty` e métodos `Connection` adicionados ao objeto. Veja a documentação [aqui](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- TTS C++ api `std::wstring` agora suporta como entrada de texto de síntese, removendo a necessidade de converter um wstring em string antes de passá-lo para o SDK. Veja os detalhes [aqui](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [ID de idioma](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) e [configuração de idioma de origem](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) já estão disponíveis.
- JavaScript: Adicionou `Connection` um recurso para se opor para passar `receivedServiceMessage`mensagens personalizadas do Serviço de Fala como retorno de chamada .
- JavaScript: Adicionado `FromHost API` suporte para facilitar o uso com contêineres on-prem e nuvens soberanas. Veja a documentação [aqui](speech-container-howto.md).
- JavaScript: Agora `NODE_TLS_REJECT_UNAUTHORIZED` honramos graças a uma contribuição de [orgads](https://github.com/orgads). Veja os detalhes [aqui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Alterações da falha**

- `OpenSSL`foi atualizado para a versão 1.1.1b e está estático ligado à biblioteca principal do Speech SDK para Linux. Isso pode causar uma quebra `OpenSSL` se a caixa `/usr/lib/ssl` de entrada não tiver sido instalada no diretório do sistema. Por favor, verifique [nossa documentação](how-to-configure-openssl-linux.md) em speech sdk documentos para trabalhar em torno do problema.
- Alteramos o tipo de dados `WordLevelTimingResult.Offset` retornado `long` para C# `WordLevelTimingResults` para permitir o acesso a quando os dados de `int` fala são maiores que 2 minutos.
- `PushAudioInputStream`e `PullAudioInputStream` agora enviar informações de cabeçalho wav para o Serviço de Fala com base, `AudioStreamFormat`opcionalmente especificado quando eles foram criados. Os clientes agora devem usar o [formato de entrada de áudio suportado](how-to-use-audio-input-streams.md). Quaisquer outros formatos obterão resultados de reconhecimento abaixo do ideal ou podem causar outros problemas. 

**Correções**

- Veja `OpenSSL` a atualização em Breaking changes acima. Corrigimos tanto um crash intermitente quanto um problema de desempenho (contenção de bloqueio sob alta carga) no Linux e Java. 
- Java: Fez melhorias no fechamento de objetos em cenários de alta concorrência.
- Reestruturou nosso pacote NuGet. Removemos as três `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` cópias de e sob pastas lib, tornando o pacote NuGet menor e mais rápido para baixar, e adicionamos cabeçalhos necessários para compilar alguns aplicativos nativos C++.
- Fixo amostras de quickstart [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Estes estavam saindo sem exibir exceção "microfone não encontrado" no Linux, MacOS, Windows.
- Acidente de SDK fixo com resultados longos de reconhecimento de fala em certos caminhos de código como [esta amostra](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Corrigimos o erro de implantação do SDK no ambiente do Azure Web App para resolver [esse problema com o cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Corrigimos um erro TTS ao usar multi `<voice>` tag ou `<audio>` tag para resolver esse problema do [cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Corrigimos um erro tts 401 quando o SDK é recuperado de suspensão.
- JavaScript: Corrigiu uma importação circular de dados de áudio graças a uma contribuição [da euirim](https://github.com/euirim). 
- JavaScript: adicionado suporte para definir propriedades de serviço, conforme adicionado em 1.7.
- JavaScript: corrigiu um problema no qual um erro de conexão poderia resultar em tentativas contínuas e mal sucedidas de reconexão de websocket.

**Exemplos**

- Adicionado amostra de reconhecimento de palavras-chave para Android [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Adicionada amostra TTS para o cenário do servidor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- A conversa ção multi-dispositivo adicionado começa rapidamente para C# e C++ [aqui](quickstarts/multi-device-conversation.md).

**Outras alterações**

- Tamanho otimizado da biblioteca principal do SDK no Android.
- O SDK em 1.9.0 e `int` `string` em diante suporta ambos e tipos no campo de versão de assinatura de voz para Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: lançamento 2019-novembro

**Novos recursos**

- Adicionada `FromHost()` uma API, para facilitar o uso com contêineres on-prem e nuvens soberanas.
- Adicionado detecção automática de linguagem de origem para reconhecimento de fala (em Java e C++)
- Objeto `SourceLanguageConfig` adicionado para reconhecimento de voz, usado para especificar idiomas de origem esperados (em Java e C++)
- Adicionado `KeywordRecognizer` suporte no Windows (UWP), Android e iOS através dos pacotes NuGet e Unity
- Adicionado API Java de conversação remota para fazer transcription de conversação em lotes assíncronos.

**Alterações da falha**

- As funcionalidades da Transcrição `Microsoft.CognitiveServices.Speech.Transcription`da Conversa ção foram movidas sob namespace .
- Parte dos métodos de Transcrição `Conversation` da Conversa são movidos para uma nova classe.
- Suporte descartado para iOs de 32 bits (ARMv7 e x86)

**Correções**

- Corrija para `KeywordRecognizer` falha se local for usado sem uma chave de assinatura de serviço de fala válida

**Exemplos**

- Amostra de Xamarin para`KeywordRecognizer`
- Amostra de unidade para`KeywordRecognizer`
- Amostras C++ e Java para detecção automática de linguagem de origem.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: lançamento 2019-setembro

**Novos recursos**

- Adicionado suporte beta para Xamarin na Universal Windows Platform (UWP), Android e iOS
- Adicionado suporte ao iOS para o Unity
- Adicionado `Compressed` suporte de entrada para ALaw, Mulaw, FLAC no Android, iOS e Linux
- Adicionado `SendMessageAsync` `Connection` em sala de aula para enviar uma mensagem ao serviço
- Adicionado `SetMessageProperty` `Connection` na classe para definir a propriedade de uma mensagem
- TTS adicionou ligações para Java (Jre e Android), Python, Swift e Objective-C
- O TTS adicionou suporte à reprodução para macOS, iOS e Android.
- Adicionadas informações de "limite de palavras" para TTS.

**Correções**

- Problema de compilação do IL2CPP fixo no Unity 2019 para Android
- Problema corrigido com cabeçalhos malformados na entrada de arquivo wav sendo processado incorretamente
- Problema corrigido com UUIDs não sendo único em algumas propriedades de conexão
- Corrigimos alguns avisos sobre especificadores de nulidade nas vinculações Swift (pode exigir pequenas alterações de código)
- Corrigimos um bug que fez com que as conexões de websocket fossem fechadas sem graça sob carga de rede
- Corrigimos um problema no Android que às vezes resulta em IDs de impressão duplicada usados por`DialogServiceConnector`
- Melhorias na estabilidade das conexões em interações multi-turno sustais e no relato de falhas (via `Canceled` eventos) quando ocorrem com`DialogServiceConnector`
- `DialogServiceConnector`sessão começa agora fornecerá adequadamente `ListenOnceAsync()` eventos, incluindo quando chamar durante um ativo`StartKeywordRecognitionAsync()`
- Abordou um acidente `DialogServiceConnector` associado às atividades que estão sendo recebidas

**Exemplos**

- Início rápido para Xamarin
- CPP Quickstart atualizado com informações do Linux ARM64
- Unity atualizado inicia rapidamente com informações do iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: Lançamento 2019-junho

**Exemplos**

- Amostras quickstart para texto para falar em UWP e unidade
- Amostra quickstart para Swift no iOS
- Amostras de unidade para reconhecimento e tradução de intenções de & de fala
- Amostras atualizadas de quickstart para`DialogServiceConnector`

**Melhorias/Alterações**

- Espaço de nome de diálogo:
  - `SpeechBotConnector` foi renomeado para `DialogServiceConnector`
  - `BotConfig` foi renomeado para `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`foi remapeado para`DialogServiceConfig::FromBotSecret()`
  - Todos os clientes existentes do Direct Line Speech continuam a ser suportados após o renomeação
- Atualize o adaptador TTS REST para suportar proxy, conexão persistente
- Melhore a mensagem de erro quando uma região inválida for passada
- Swift/Objective-C:
  - Relatórios de erro aprimorados: Métodos que podem resultar em um erro `NSError` estão agora presentes em duas versões: uma que expõe um objeto para manipulação de erros e outra que levanta uma exceção. Os primeiros estão expostos a Swift. Essa mudança requer adaptações ao código Swift existente.
  - Melhor manuseio de eventos

**Correções**

- Correção para TTS: onde `SpeakTextAsync` o futuro retornou sem esperar até que o áudio tenha concluído a renderização
- Corrija para strings de empacotamento em C# para habilitar o suporte completo ao idioma
- Corrija para o problema do aplicativo núcleo .NET para carregar a biblioteca principal com a estrutura de destino net461 em amostras
- Corrija para problemas ocasionais para implantar bibliotecas nativas na pasta de saída em amostras
- Fixação para fechamento do soquete web de forma confiável
- Corrigir para possível falha ao abrir uma conexão sob carga muito pesada no Linux
- Corrigir para metadados ausentes no pacote de framework para macOS
- Corrija para `pip install --user` problemas com no Windows

## <a name="speech-sdk-151"></a>Fala SDK 1.5.1

Esta é uma correção de correção de bugs e afeta apenas o SDK nativo/gerenciado. Não está afetando a versão JavaScript do SDK.

**Correções**

- Corrigir a assinatura de assinatura quando usado com transcrição de conversação.
- Corrigir bug em localização de palavras-chave para assistentes de voz.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: Lançamento 2019-Maio

**Novos recursos**

- O spotting de palavras-chave (KWS) já está disponível para Windows e Linux. A funcionalidade KWS pode funcionar com qualquer tipo de microfone, o suporte oficial ao KWS, no entanto, está atualmente limitado aos arrays de microfone encontrados no hardware Do Cingido DK do Azure ou no SDK speech Devices.
- A funcionalidade da dica de frase está disponível através do SDK. Para mais informações, consulte [aqui](how-to-phrase-lists.md).
- A funcionalidade de transcrição da conversa está disponível através do SDK. Veja [aqui](conversation-transcription-service.md).
- Adicione suporte para assistentes de voz usando o canal Direct Line Speech.

**Exemplos**

- Adicionado amostras para novos recursos ou novos serviços suportados pelo SDK.

**Melhorias/Alterações**

- Adicionadas várias propriedades de reconhecimento para ajustar o comportamento do serviço ou os resultados do serviço (como palavrões mascarados e outros).
- Agora você pode configurar o ressartivista através das propriedades `FromEndpoint`de configuração padrão, mesmo se você criou o ressartilista .
- Objetivo-C: `OutputFormat` a propriedade `SPXSpeechConfiguration`foi adicionada a .
- O SDK agora suporta o Debian 9 como uma distribuição Linux.

**Correções**

- Corrigimos um problema em que o recurso do alto-falante foi destruído muito cedo no texto-para-fala.

## <a name="speech-sdk-142"></a>Fala SDK 1.4.2

Esta é uma correção de correção de bugs e afeta apenas o SDK nativo/gerenciado. Não está afetando a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>Fala SDK 1.4.1

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

- Evite que o web pack seja carregado https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: lançamento 2019-abril

**Novos recursos**

- O SDK agora suporta o serviço de texto para fala como uma versão beta. É suportado no Windows e Linux Desktop de C++ e C#. Para obter mais informações, verifique a [visão geral do texto para a fala](text-to-speech.md#get-started).
- O SDK agora suporta arquivos de áudio MP3 e Opus/OGG como arquivos de entrada de fluxo. Este recurso está disponível apenas no Linux de C++ e C# e está atualmente em beta (mais detalhes [aqui](how-to-use-codec-compressed-audio-input-streams.md)).
- O Speech SDK for Java, .NET core, C++ e Objective-C ganharam suporte ao macOS. O suporte Objective-C para macOS está atualmente em beta.
- iOS: O Speech SDK para iOS (Objective-C) também é publicado como um CocoaPod.
- JavaScript: Suporte para microfone não padrão como um dispositivo de entrada.
- JavaScript: Suporte proxy para Node.js.

**Exemplos**

- Foram adicionadas amostras para o uso do Speech SDK com C++ e com Objective-C no macOS.
- Foram adicionadas amostras que demonstrem o uso do serviço texto-para-fala.

**Melhorias/Alterações**

- Python: Propriedades adicionais dos resultados de `properties` reconhecimento são agora expostas através da propriedade.
- Para obter desenvolvimento adicional e suporte a depuração, você pode redirecionar as informações de registro e diagnóstico do SDK em um arquivo de log (mais detalhes [aqui](how-to-use-logging.md)).
- JavaScript: Melhore o desempenho do processamento de áudio.

**Correções**

- Mac/iOS: Um bug que levou a uma longa espera quando uma conexão com o serviço Speech não pôde ser estabelecida foi corrigido.
- Python: melhore o manuseio de erros para argumentos em retornos python.
- JavaScript: Corrigiu o relatório de estado errado para a fala encerrado na Sessão de Solicitação.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: atualização 2019-fevereiro

Esta é uma correção de correção de bugs e afeta apenas o SDK nativo/gerenciado. Não está afetando a versão JavaScript do SDK.

**Correção de bugs**

- Corrigimos um vazamento de memória ao usar a entrada do microfone. A entrada de fluxo ou arquivo não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: lançamento 2019-fevereiro

**Novos recursos**

- O Speech SDK suporta a seleção do microfone de entrada através da `AudioConfig` classe. Isso permite que você transmita dados de áudio para o serviço Speech a partir de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve a [seleção](how-to-select-audio-input-devices.md)de dispositivos de entrada de áudio . Este recurso ainda não está disponível no JavaScript.
- O Speech SDK agora dá suporte ao Unity em uma versão beta. Forneça feedback através da seção de problemas no [repositório de amostras do GitHub](https://aka.ms/csspeech/samples). Essa versão dá suporte ao Unity no Windows x86 e x64 (área de trabalho ou aplicativos da Plataforma Universal do Windows) e Android (ARM32/64, x86). Mais informações estão disponíveis em nosso [início rápido do Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- O `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` arquivo (enviado em versões anteriores) não é mais necessário. A funcionalidade agora está integrada ao SDK principal.

**Exemplos**

O novo conteúdo a seguir está disponível no nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

- Amostras adicionais para `AudioConfig.FromMicrophoneInput`.
- Mais exemplos do Python para conversão e reconhecimento de intenção.
- Amostras adicionais `Connection` para o uso do objeto no iOS.
- Exemplos adicionais de Java para tradução com saída de áudio.
- Novo exemplo para usar a [API REST de Transcrição de Lote](batch-transcription.md).

**Melhorias/Alterações**

- Python
  - Verificação aprimorada de parâmetros `SpeechConfig`e mensagens de erro em .
  - Adicione suporte `Connection` para o objeto.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O Speech SDK para Python está fora do beta.
- iOS
  - O SDK agora é construído com relação ao SDK versão 12.1 do iOS.
  - O SDK agora dá suporte a iOS versões 9.2 e posteriores.
  - Melhore a documentação de referência e conserte vários nomes de propriedade.
- JavaScript
  - Adicione suporte `Connection` para o objeto.
  - Adicione arquivos de definição de tipo para JavaScript agrupado
  - Suporte inicial e implementação para dicas de frase.
  - Retornar a coleção de propriedades com o serviço de JSON para reconhecimento
- DLLs do Windows agora contêm um recurso de versão.
- Se você criar `FromEndpoint` um reconhista, poderá adicionar parâmetros diretamente à URL do ponto final. O `FromEndpoint` uso não pode configurar o reconhecedor através das propriedades de configuração padrão.

**Correções**

- Nome de usuário de proxy e senha de proxy vazios não foram tratados corretamente. Com esta versão, se você definir o nome de usuário de proxy e a senha de proxy como uma cadeia de caracteres vazia, eles não serão enviados ao conectarem-se ao proxy.
- As SessionIds criadas pelo SDK nem sempre eram realmente aleatórias para alguns idiomas&nbsp;/ambientes. Adicionada inicialização aleatória do gerador para corrigir esse problema.
- Melhore o tratamento do token de autorização. Se você quiser usar um token `SpeechConfig` de autorização, especifique no e deixe a chave de assinatura vazia. Em seguida, crie o reconhecedor como de costume.
- Em alguns `Connection` casos, o objeto não foi liberado corretamente. Esse problema foi corrigido.
- O exemplo de JavaScript foi corrigido para dar suporte para saída de áudio para síntese de conversão também no Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

- Acionar o final do fluxo no turn.end, não no speech.end.
- Consertar um bug na bomba de áudio que não agendou o próximo envio em caso de falha do envio atual.
- Consertar reconhecimento contínuo com o token de autenticação.
- Correção de bug para diferentes reconhecedores/pontos de extremidade.
- Melhorias na documentação.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: lançamento 2018-dezembro

**Novos recursos**

- Python
  - A versão Beta do suporte do Python (3.5 e posterior) está disponível com esta versão. Para obter mais informações, consulte aqui](quickstart-python.md).
- JavaScript
  - O SDK de Fala para o JavaScript tem sido livre. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Agora damos suporte a node. js, mais informações podem ser encontradas [aqui](quickstart-js-node.md).
  - A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a tampa.
- Objeto `Connection`
  - A `Recognizer`partir do , `Connection` você pode acessar um objeto. Esse objeto permite iniciar a conexão de serviço e inscrever-se para se conectar e desconectar de eventos explicitamente.
    (Este recurso ainda não está disponível em JavaScript e Python.)
- Suporte para Ubuntu 18.04.
- Android
  - Suporte do ProGuard habilitado durante a geração de APK.

**Melhorias**

- Melhorias no uso de thread interno, reduzindo o número de threads, bloqueios e exclusões mútuas.
- Relatório/informações de erros aprimorados. Em vários casos, as mensagens de erro não foram propagadas até o fim.
- As dependências de desenvolvimento atualizadas do JavaScript para usar módulos atualizados.

**Correções**

- Vazamentos de memória fixos devido `RecognizeAsync`a uma incompatibilidade de tipo em .
- Em alguns casos, as exceções foram sendo vazadas.
- Corrigindo o vazamento de memória em argumentos de evento de tradução.
- Corrigido um problema de bloqueio na reconexão longa de sessões em execução.
- Corrigimos um problema que poderia levar à falta de resultado final para traduções com falha.
- C#: Se `async` uma operação não fosse aguardada no segmento principal, era possível que o reconhecedor pudesse ser eliminado antes que a tarefa de async fosse concluída.
- Java: Corrigimos um problema que resultou em uma falha do Java VM.
- Objetivo-C: Mapeamento de enum fixo; RecognizedIntent foi devolvido `RecognizingIntent`em vez de .
- JavaScript: Defina o formato de `SpeechConfig`saída padrão para 'simples' em .
- JavaScript: Removendo a inconsistência entre propriedades no objeto de configuração em JavaScript e outras línguas.

**Exemplos**

- Atualizei e corrigiu várias amostras (por exemplo, vozes de saída para tradução, etc.).
- Adicionados exemplos do Node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK de Fala 1.1.0

**Novos recursos**

- Suporte para Android x86/x64.
- Suporte proxy: `SpeechConfig` No objeto, agora você pode chamar uma função para definir as informações de proxy (nome de host, porta, nome de usuário e senha). Este recurso ainda não está disponível no iOS.
- Melhor código de erro e mensagens. Se um reconhecimento retornou um erro, isso já definiu `Reason` (no evento cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor retornou informações de erro adicionais com o erro relatado, agora ele estará disponível nos novos membros.

**Melhorias**

- Adicionada verificação adicional na configuração do reconhecedor e adicionada outra mensagem de erro.
- Manipulação aprimorada de silêncio de longa duração no meio de um arquivo de áudio.
- Pacote NuGet: para projetos do .NET Framework, ele impede a construção com a configuração AnyCPU.

**Correções**

- Corrigido várias exceções encontradas em reconhecedores. Além disso, exceções são capturadas e convertidas em `Canceled` evento.
- Corrigir um vazamento de memória no gerenciamento de propriedades.
- Corrigido o erro no qual um arquivo de entrada de áudio poderia travar o reconhecedor.
- Corrigido um bug no qual os eventos podiam ser recebidos após um evento de parada da sessão.
- Corrigidas algumas condições de corrida no threading.
- Corrigido um problema de compatibilidade do iOS que poderia resultar em uma falha.
- Melhorias de estabilidade para suporte de microfone Android.
- Corrigido um erro em que um reconhecedor em JavaScript ignoraria o idioma de reconhecimento.
- Corrigimos um bug `EndpointId` impedindo a configuração (em alguns casos) no JavaScript.
- Alterou a ordem do parâmetro em AddIntent em JavaScript e adicionou a assinatura JavaScript ausente. `AddIntent`

**Exemplos**

- Adicionadas amostras C++ e C# para o uso do fluxo de puxar e empurrar no [repositório da amostra](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK de Fala 1.0.1

Melhorias na confiabilidade e correções de bugs:

- Corrigido erro fatal potencial devido à condição de corrida no reconhecedor de descarte
- Corrigido erro fatal potencial no caso de propriedades não definidas.
- Adicionado erro adicional e verificação de parâmetros.
- Objective-C: corrigido possível erro fatal causado por substituição de nome em NSString.
- Objective-C: visibilidade ajustada da API
- JavaScript: corrigido em relação a eventos e cargas.
- Melhorias na documentação.

Em nosso [repositório de exemplos](https://aka.ms/csspeech/samples), um novo exemplo para JavaScript foi adicionado.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de Fala dos Serviços Cognitivos 1.0.0: versão de setembro de 2018

**Novos recursos**

- Suporte para Objective-C no iOS. Confira nosso [Início Rápido do Objective-C para iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Suporte para JavaScript no navegador. Confira nosso [Início Rápido do JavaScript](quickstart-js-browser.md).

**Alterações da falha**

- Com esta versão, uma série de mudanças de quebra são introduzidas.
  Verifique [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Fala dos Serviços Cognitivos 0.6.0: versão de agosto de 2018

**Novos recursos**

- Os aplicativos UWP criados com o SDK de Fala agora podem ser aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
  Confira o [Início Rápido do UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
- Experimental: dê suporte Java 8 no Windows (64 bits) e no Linux (Ubuntu 16.04 x64).
  Confira o [java runtime environment quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Alteração funcional**

- Expor informações de detalhe de erro adicionais sobre erros de conexão.

**Alterações da falha**

- No Java (Android), a função `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` não requer mais um parâmetro de caminho. Agora, o caminho é detectado automaticamente em todas as plataformas com suporte.
- O get-accessor da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções**

- Em Java, o resultado da síntese de áudio no reconhecedor de tradução agora está implementado.
- Foi corrigido um bug que podia causar threads inativos e um grande número de soquetes abertos e não usados.
- Foi corrigido um problema em que o reconhecimento de execução longa podia terminar no meio da transmissão.
- Corrigida uma condição de corrida no desligamento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Fala dos Serviços Cognitivos 0.5.0: versão de julho de 2018

**Novos recursos**

- Suporte a plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o [Início Rápido para Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Suporte para .NET Standard 2.0 no Windows. Confira o [Início Rápido para .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte a UWP no Windows (versão 1709 ou posterior).
  - Confira o [Início Rápido do UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Observação: os aplicativos UWP compilados com o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
- Suporte ao reconhecimento de execução longa com reconexão automática.

**Alterações funcionais**

- O `StartContinuousRecognitionAsync()` dá suporte ao reconhecimento de execução longa.
- O resultado do reconhecimento contém mais campos. Eles são deslocados do início do áudio e da duração (ambos em tiques) do texto reconhecido e dos valores adicionais que representam o status de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações da falha**

- Eventos de `NoMatch` reconhecimento: o tipo `Error` de evento foi mesclado no evento.
- SpeechOutputFormat em C# foi `OutputFormat` renomeado para permanecer alinhado com C++.
- O tipo de retorno de alguns métodos da interface `AudioInputStream` foi um pouco alterado:
  - Em Java, o método `read` agora retorna `long` em vez de `int`.
  - Em C#, o método `Read` agora retorna `uint` em vez de `int`.
  - Em C++, os métodos `Read` e `GetFormat` agora retornam `size_t` em vez de `int`.
- C++: as instâncias de fluxos de entrada de áudio agora podem ser passadas apenas como um `shared_ptr`.

**Correções**

- Foram corrigidos os valores retornados incorretos no resultado quando `RecognizeAsync()` atinge o tempo limite.
- A dependência das bibliotecas do Media Foundation no Windows foi removida. O SDK agora usa as APIs Core Audio.
- Correção da documentação: uma página de [regiões](regions.md) foi adicionada para descrever as regiões com suporte.

**Problema conhecido**

- O SDK de Fala para Android não relata os resultados da síntese de fala para tradução. Esse problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Fala de Serviços Cognitivos 0.4.0: versão de junho de 2018

**Alterações funcionais**

- AudioInputStream

  Agora, um reconhecedor pode consumir um fluxo como a fonte de áudio. Para obter mais detalhes, confira o [guia de instruções](how-to-use-audio-input-streams.md) relacionado.

- Formato de saída detalhado

  Ao criar um `SpeechRecognizer`, você pode solicitar o formato de saída `Detailed` ou `Simple`. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma léxica bruta, forma normalizada e forma normalizada com obscenidades mascaradas.

**Mudança de ruptura**

- Alterado para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` em C#.

**Correções**

- Foi corrigido um possível problema de retorno de chamada na camada USP durante o desligamento.
- Se um reconhecedor consumir um arquivo de entrada de áudio, ele manteve o identificador de arquivo por mais tempo do que o necessário.
- Foram removidos vários deadlocks entre a bomba de mensagens e o reconhecedor.
- Dispare um resultado `NoMatch` quando o tempo de resposta do serviço esgotar.
- As bibliotecas do Media Foundation no Windows são carregadas com atraso. Essa biblioteca é necessária apenas para entrada do microfone.
- A velocidade de carregamento de dados de áudio é limitada a duas vezes a velocidade do áudio original.
- No Windows, agora os assemblies .NET em C# têm nomes fortes.
- Correção de documentação: `Region` são as informações necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e são atualizados constantemente. Para obter o último conjunto de exemplos, confira o [Repositório GitHub de exemplos do SDK de Fala](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Fala de Serviços Cognitivos 0.2.12733: versão de maio de 2018

Esta versão é a primeira versão prévia pública do SDK de Fala dos Serviços Cognitivos.
