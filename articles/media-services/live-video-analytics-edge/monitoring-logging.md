---
title: Monitoramento e registro em log – Azure
description: Este artigo fornece uma visão geral da análise de vídeo ao vivo em IoT Edge monitoramento e registro em log.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 807b0623159e0b50285b89da2835e9dd6cb037aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260569"
---
# <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Neste artigo, você aprenderá sobre como você pode receber eventos da análise de vídeo ao vivo no módulo IoT Edge para monitoramento remoto. 

Você também aprenderá como é possível controlar os logs gerados pelo módulo.

## <a name="taxonomy-of-events"></a>Taxonomia de eventos

A análise de vídeo ao vivo em IoT Edge emite eventos ou dados de telemetria de acordo com a taxonomia a seguir.

![Análise de vídeo ao vivo em IoT Edge esquema de telemetria](./media/telemetry-schema/taxonomy.png)

* Operacional: eventos gerados como parte das ações executadas por um usuário ou durante a execução de um [grafo de mídia](media-graph-concept.md).
   
   * Volume: espera-se que seja baixo (algumas vezes por minuto ou até mesmo uma taxa mais baixa).
   * Exemplos:

      Gravação iniciada (abaixo), gravação interrompida
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnóstico: eventos que ajudam a diagnosticar problemas e/ou problemas com o desempenho.

   * Volume: pode ser alto (várias vezes por minuto).
   * Exemplos:
   
      Informações de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) RTSP (abaixo) ou lacunas no feed de vídeo de entrada.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Análise: eventos que são gerados como parte da análise de vídeo.

   * Volume: pode ser alto (várias vezes por minuto ou mais vezes).
   * Exemplos:
      
      Movimento detectado (abaixo), resultado da inferência.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
Os eventos emitidos pelo módulo são enviados para o [Hub de IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)e, daí, podem ser roteados para outros destinos. 

## <a name="controlling-events"></a>Controle de eventos

Você pode usar as propriedades do módulo CTRL a seguir, conforme documentado no [esquema JSON do módulo](module-twin-configuration-schema.md)//para controlar os eventos de diagnóstico e operacionais que são publicados pela análise de vídeo ao vivo no módulo IOT Edge.

`diagnosticsEventsOutputName`– incluir e fornecer (qualquer) valor para essa propriedade, para obter eventos de diagnóstico do módulo. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos de diagnóstico.
   
`operationalEventsOutputName`– incluir e fornecer (qualquer) valor para essa propriedade, para obter eventos operacionais do módulo. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos operacionais.
   
Os eventos de análise são gerados por nós como o processador de detecção de movimento ou o processador de extensão HTTP, e o coletor de Hub IoT é usado para enviá-los para o Hub de IoT Edge. 

Você pode controlar o [Roteamento de todos os eventos acima](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) por meio de uma propriedade desejada do módulo de $edgeHub "d" (no manifesto de implantação):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

No acima, lvaEdge é o nome da análise de vídeo ao vivo no módulo IoT Edge e a regra de roteamento segue o esquema definido em [declarar rotas](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

> [!NOTE]
> Para garantir que os eventos de análise alcancem o Hub de IoT Edge, precisa haver um nó de coletor de Hub IoT downstream de qualquer nó de processador de detecção de movimento e/ou qualquer nó de processador de extensão HTTP.

## <a name="event-schema"></a>Esquema do evento

Os eventos originam-se no dispositivo de borda e podem ser consumidos na borda ou na nuvem. Eventos gerados pela análise de vídeo ao vivo em IoT Edge estão em conformidade com o [padrão de mensagens de streaming](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) estabelecido pelo Hub IOT do Azure, com propriedades do sistema, propriedades do aplicativo e um corpo.

### <a name="summary"></a>Resumo

Cada evento, quando observado por meio do Hub IoT, terá um conjunto de propriedades comuns, conforme descrito abaixo.

|Property   |Tipo de propriedade| Tipo de Dados   |Descrição|
|---|---|---|---|
|message-id |sistema |guid|  ID de evento exclusivo.|
|topic| applicationproperty |string|    Azure Resource Manager caminho para a conta dos serviços de mídia.|
|subject|   applicationproperty |string|    Sub-caminho para a entidade que emite o evento.|
|eventTime| applicationproperty|    string| Hora em que o evento foi gerado.|
|eventType| applicationproperty |string|    Identificador de tipo de evento (veja abaixo).|
|body|body  |objeto|    Dados de evento específicos.|
|dataVersion    |applicationproperty|   string  |{Major}. Secundária|

### <a name="properties"></a>Propriedades

#### <a name="message-id"></a>message-id

Identificador global exclusivo do evento (GUID)

#### <a name="topic"></a>topic

Representa a conta do serviço de mídia do Azure associada ao grafo.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Entidade que está emitindo o evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A propriedade Subject permite que eventos genéricos sejam mapeados para seu módulo de geração. Por exemplo, no caso de nome de usuário ou senha RTSP inválido, o evento gerado estaria `Microsoft.Media.Graph.Diagnostics.ProtocolError` no `/graphInstances/myGraph/sources/myRtspSource` nó.

#### <a name="event-types"></a>Tipos de evento

Os tipos de evento são atribuídos a um namespace de acordo com o esquema a seguir:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classes de evento

|Nome da Classe|Descrição|
|---|---|
|Análise  |Eventos gerados como parte da análise de conteúdo.|
|Diagnósticos    |Eventos que auxiliam no diagnóstico de problemas e desempenho.|
|Operacional    |Eventos gerados como parte da operação de recurso.|

Os tipos de evento são específicos para cada classe de evento.

Exemplos:

* Microsoft. Media. Graph. Analytics. inferência
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Hora do evento

A hora do evento é descrita na cadeia de caracteres ISO8601 e a hora em que o evento ocorreu.

## <a name="logging"></a>Registro em log

Assim como com outros módulos IoT Edge, você também pode [examinar os logs de contêiner](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) no dispositivo de borda. As informações gravadas nos logs podem ser controladas pelas seguintes propriedades de [MyModule](module-twin-configuration-schema.md) :

* logLevel

   * Os valores permitidos são Verbose, informações, aviso, erro, nenhum.
   * O valor padrão é informações – os logs conterão erro, aviso e informações. mensagens.
   * Se você definir o valor como Warning, os logs conterão mensagens de erro e de aviso
   * Se você definir o valor como erro, os logs conterão apenas mensagens de erro.
   * Se você definir o valor como nenhum, nenhum log será gerado (isso não é recomendado).
   * Você só deve usar detalhado se precisar compartilhar logs com o suporte do Azure para diagnosticar um problema.
* logCategories

   * Uma lista separada por vírgulas de um ou mais dos seguintes itens: Application, Events, MediaPipeline.
   * Padrão: aplicativo, eventos.
   * Aplicativo – essas são informações de alto nível do módulo, como mensagens de inicialização de módulo, erros de ambiente e chamadas de método direta.
   * Eventos – esses são todos os eventos que foram descritos anteriormente neste artigo.
   * MediaPipeline – esses são alguns logs de baixo nível que podem oferecer Insight ao solucionar problemas, como dificuldades ao estabelecer uma conexão com uma câmera compatível com RTSP.
   
### <a name="generating-debug-logs"></a>Gerando logs de depuração

Em determinados casos, talvez seja necessário gerar logs mais detalhados do que os descritos acima, para ajudar o suporte do Azure a resolver um problema. Há duas etapas para fazer isso.

Primeiro, você [vincula o armazenamento do módulo ao armazenamento do dispositivo](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module#link-module-storage-to-device-storage) por meio de. Se você examinar um [modelo de manifesto de implantação](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) do início rápido, verá:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Acima permite que o módulo do Edge grave logs no caminho de armazenamento do (dispositivo) "/var/local/mediaservices/". Se você adicionar a seguinte propriedade desejada ao módulo:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Em seguida, o módulo gravará os logs de depuração em um formato binário para o caminho de armazenamento (dispositivo)/var/local/mediaservices/debuglogs/, que você pode compartilhar com o suporte do Azure.

## <a name="faq"></a>Perguntas frequentes

[Perguntas frequentes](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Próximas etapas

[Gravação de vídeo contínua](continuous-video-recording-tutorial.md)
