---
title: Transcrição de Conversa Assíncrona (Preview) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar transcrição de conversaas assíncronas usando o serviço Speech. Disponível apenas para Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366605"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transcrição de Conversa Assíncrona (Visualização)

Neste artigo, a Transcrição de Conversa assíncrona é demonstrada usando a API **RemoteConversationTranscriptionClient.** Se você configurou a Transcrição de Conversação para fazer `conversationId`transcrição assíncrona e `conversationId` tiver uma, você pode obter a transcrição associada a isso usando a API **RemoteConversationTranscriptionClient.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Assíncrono vs. em tempo real + assíncrono

Com transcrição assíncrona, você transmite o áudio da conversa, mas não precisa de uma transcrição devolvida em tempo real. Em vez disso, depois que `conversationId` `Conversation` o áudio é enviado, use o of para consultar o status da transcrição assíncrona. Quando a transcrição assíncrona estiver pronta, `RemoteConversationTranscriptionResult`você terá um.

Com tempo real mais assíncrono, você recebe a transcrição em tempo real, mas `conversationId` também recebe a transcrição consultando com o (semelhante ao cenário assíncrono).

Duas etapas são necessárias para realizar a transcrição assíncrona. O primeiro passo é carregar o áudio, escolhendo apenas assíncrono ou em tempo real, além de assíncrono. O segundo passo é obter os resultados da transcrição.

## <a name="upload-the-audio"></a>Faça upload do áudio

Antes que a transcrição assíncrona possa ser realizada, você precisa enviar o áudio para o Serviço de Transcrição de Conversas usando o Cliente De Fala Cognitiva da Microsoft SDK (versão 1.8.0 ou superior).

Este código de exemplo mostra como criar transcritor de conversação para o modo somente assíncrono. Para transmitir áudio para o transcritor, você precisará adicionar código de streaming de áudio derivado de [conversas transcritor em tempo real com o Speech SDK](how-to-use-conversation-transcription-service.md). Consulte a seção **Limitações** desse tópico para ver as APIs de plataformas e idiomas suportadas.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Se você quiser _em_ tempo real mais assíncrono, comente e não comente as linhas de código apropriadas da seguinte forma:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Obter resultados de transcrição

Esta etapa obtém os resultados de transcrição assíncrona, mas assume que qualquer processamento em tempo real que você possa ter exigido é feito em outro lugar. Para obter mais informações, consulte [Transcrever conversas em tempo real com o Speech SDK](how-to-use-conversation-transcription-service.md).

Para o código mostrado aqui, você precisa de **conversa remota versão 1.8.0**, suportado apenas para Java (1.8.0 ou superior) no Windows, Linux e Android (nível DePI 26 ou superior).

### <a name="obtaining-the-client-sdk"></a>Obtenção do Cliente SDK

Você pode obter **conversa remota** editando seu arquivo pom.xml da seguinte forma.

1. No final do arquivo, antes `</project>`da tag `repositories` de fechamento, crie um elemento com uma referência ao repositório Maven para o Speech SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Adicione também `dependencies` um elemento, com o remoteconversation-client-sdk 1.8.0 como uma dependência:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Salvar as alterações

### <a name="sample-transcription-code"></a>Código de transcrição da amostra

Depois de `conversationId`ter o , criar um cliente de transcrição de conversa remota **RemoteConversationTranscriptionClient** no aplicativo cliente para consultar o status da transcrição assíncrona. Use o método **getTranscriptionOperation** no **RemoteConversationTranscriptionClient** para obter um objeto [PollerFlux.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) O objeto PollerFlux terá informações sobre o status de operação remota **RemoteConversationTranscriptionOperation** e o resultado final **RemoteConversationTranscriptionResult**. Uma vez que a operação tenha terminado, obtenha **RemoteConversationTranscriptionResult** chamando **getFinalResult** em um [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). Neste código, simplesmente imprimimos o conteúdo do resultado para a saída do sistema.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossas amostras no GitHub](https://aka.ms/csspeech/samples)
