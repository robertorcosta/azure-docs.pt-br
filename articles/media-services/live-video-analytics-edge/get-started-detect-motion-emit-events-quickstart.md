---
title: Introdução à Análise de Vídeo ao vivo no IoT Edge – Azure
description: Este guia de início rápido mostra como começar a usar a Análise de Vídeo ao vivo no IoT Edge e detectar o movimento em um fluxo de vídeo ao vivo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261560"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Início Rápido: Introdução – Análise de Vídeo ao vivo no IoT Edge

Este guia de início rápido orienta você pelas etapas para começar a usar a Análise de Vídeo ao vivo no IoT Edge. Ele usa uma VM do Azure como dispositivo IoT Edge e uma transmissão de vídeo ao vivo simulada. Depois de concluir as etapas de instalação, você poderá executar um fluxo de vídeo ao vivo simulado por meio de um grafo de mídia que detecta e relata qualquer movimento nesse fluxo. O diagrama abaixo mostra uma representação gráfica desse grafo de mídia.

![Análise de Vídeo ao Vivo com base na detecção de movimento](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com a [extensão do Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* A rede à qual seu computador de desenvolvimento está conectado deve permitir o protocolo AMQP pela porta 5671 (para que o Azure IoT Tools possa se comunicar com o Hub IoT do Azure).

> [!TIP]
> Ao instalar a extensão do Azure IoT Tools, talvez você receba uma solicitação para instalar o Docker. Fique à vontade para ignorá-la.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Os serviços do Azure a seguir são necessários para este tutorial.

* Hub IoT
* Conta de armazenamento
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

Para este guia de início rápido, recomendamos que você use o [script de instalação de recursos de Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar os recursos do Azure mencionados acima em sua assinatura do Azure. Para isso, siga as etapas abaixo:

1. Navegue até https://shell.azure.com.
1. Se esta for a primeira vez que você estiver usando Cloud Shell, você será solicitado a selecionar uma assinatura a fim de criar uma conta de armazenamento e um compartilhamento de Arquivos do Microsoft Azure. Selecione "Criar armazenamento" para criar uma conta de armazenamento para armazenar suas informações de sessão do Cloud Shell. Essa conta de armazenamento é separada daquela que o script criará para usar com sua conta dos Serviços de Mídia do Azure.
1. Selecione "Bash" como seu ambiente na lista suspensa no lado esquerdo da janela do shell.

    ![Seletor de ambiente](./media/quickstarts/env-selector.png)

1. Executar o seguinte comando

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Se o script for concluído com êxito, você verá todos os recursos mencionados acima em sua assinatura. Como parte da saída do script, uma tabela de recursos será gerada, que listará o nome do Hub IoT. Procure o tipo de recurso **"Microsoft.Devices/IotHubs"** e anote o nome. Você precisará dele na próxima etapa. O script também vai gerar alguns arquivos de configuração no diretório ~/clouddrive/lva-sample/ directory – você precisará deles posteriormente no guia de início rápido.

## <a name="deploy-modules-on-your-edge-device"></a>Implantar módulos no dispositivo de borda

Execute o comando a seguir no Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

O comando acima implantará os seguintes módulos no dispositivo de borda (a VM do Linux):

* Análise de Vídeo ao vivo no IoT Edge (nome do módulo "lvaEdge")
* Simulador RTSP (nome do módulo "rtspsim")

O módulo do simulador RTSP simula um fluxo de vídeo ao vivo usando um arquivo de vídeo armazenado que foi copiado para o dispositivo de borda quando você executou o [script de instalação dos recursos de Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Neste estágio, os módulos estão implantados, mas nenhum gráfico de mídia está ativo.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Configurar a extensão Azure IoT Tools no Visual Studio Code

Inicie o Visual Studio Code e siga as instruções abaixo para se conectar ao Hub IoT do Azure usando a extensão Azure IoT Tools.

1. Navegue até a guia Explorer no Visual Studio Code por meio de **Exibição** > **Explorer** ou simplesmente pressione (Ctrl + Shift + E).
1. Na guia Explorer, clique em "Hub IoT do Azure" no canto inferior esquerdo.
1. Clique no ícone Mais Opções para ver o menu de contexto e selecione a opção "Definir Cadeia de Conexão do Hub IoT".
1. Uma caixa de diálogo de entrada será exibida e, em seguida, insira a Cadeia de Conexão do Hub IoT. Você pode obter a cadeia de conexão para o Hub IoT de ~/clouddrive/lva-sample/appsettings.json no Cloud Shell.
1. Se a conexão for realizada com sucesso, a lista de dispositivos de borda será mostrada. Deve haver pelo menos um dispositivo, chamado "lva-sample-device".
1. Agora você pode gerenciar seus dispositivos do IoT Edge e interagir com o Hub IoT do Azure por meio do menu de contexto.
1. Você pode exibir os módulos implantados no dispositivo de borda expandindo o nó Módulos em "lva-sample-device".

    ![Nó lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Usar métodos diretos

Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando os métodos diretos dele. Leia [Métodos Diretos para Análise de Vídeo ao vivo no IoT Edge](direct-methods.md) para entender todos os métodos diretos fornecidos pelo módulo. 

### <a name="invoke-graphtopologylist"></a>Invocar GraphTopologyList
Isso enumera todas as [topologias de grafo](media-graph-concept.md#media-graph-topologies-and-instances) no módulo.

1. Clique com o botão direito do mouse no módulo "lvaEdge" e selecione "Invocar Método Direto do Módulo" no menu de contexto.
1. Você verá uma caixa de edição aparecer no meio, na parte superior, da janela do Visual Studio Code. Insira "GraphTopologyList" na caixa de edição e pressione Enter.
1. Em seguida, copie e cole o conteúdo JSON abaixo na caixa de edição e pressione Enter.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Em alguns segundos, você verá a janela SAÍDA no pop-up do Visual Studio Code com a resposta a seguir

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    A resposta acima é esperada pois nenhuma topologia de grafo foi criada.
    

### <a name="invoke-graphtopologyset"></a>Invocar GraphTopologySet

Usando as mesmas etapas que aquelas descritas para invocar GraphTopologyList, você pode invocar GraphTopologySet para definir uma [topologia de grafo](media-graph-concept.md#media-graph-topologies-and-instances) usando o JSON a seguir como o conteúdo.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


O conteúdo JSON acima resulta na criação de uma topologia de grafo que define três parâmetros (dois dos quais têm valores padrão). A topologia tem um nó de origem (origem RTSP), um nó de processador (processador de detecção de movimento) e um nó de coletor (coletor do Hub IoT).

Em poucos segundos, você verá a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

O status retornado é 201, indicando que uma nova topologia foi criada. Experimente o seguinte como as próximas etapas:

* Invoque GraphTopologySet novamente e observe que o código de status retornado é 200. O código de status 200 indica que uma topologia existente foi atualizada com êxito.
* Invoque GraphTopologySet novamente, mas altere a cadeia de caracteres de descrição. Observe que o código de status na resposta é 200 e se a descrição foi atualizada para o novo valor.
* Invoque GraphTopologyList conforme descrito na seção anterior e observe que agora você pode ver a topologia "MotionDetection" no conteúdo retornado.

### <a name="invoke-graphtopologyget"></a>Invocar GraphTopologyGet

Agora invoque GraphTopologyGet com o conteúdo a seguir

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, você deverá ver a reposta a seguir na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Observe o seguinte no conteúdo de resposta:

* O código de status é 200, indicando êxito.
* O conteúdo tem o carimbo de data/hora "created" e "lastModified".

### <a name="invoke-graphinstanceset"></a>Invocar GraphInstanceSet

Em seguida, crie uma instância de grafo que faça referência à topologia do grafo acima. Conforme explicado [aqui](media-graph-concept.md#media-graph-topologies-and-instances), as instâncias de grafo permitem analisar transmissões de vídeo ao vivo de muitas câmeras com a mesma topologia de grafo.

Invoque o método direto GraphInstanceSet com o conteúdo a seguir.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Observe o seguinte:

* O conteúdo acima especifica o nome da topologia (MotionDetection) para o qual a instância precisa ser criada.
* O conteúdo contém um valor de parâmetro para "rtspUrl", que não tinha um valor padrão no conteúdo da topologia do grafo.

Em poucos segundos, você verá a resposta a seguir na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Observe o seguinte no conteúdo de resposta:

* O código de status é 201, indicando que uma instância foi criada.
* O estado é "Inativo", indicando que a instância do grafo foi criada, mas não ativada. Para obter mais informações, confira [estados de grafo de mídia](media-graph-concept.md).

Experimente o seguinte como as próximas etapas:

* Invoque GraphInstanceSet novamente com o mesmo conteúdo e observe que o código de status retornado agora é 200.
* Invoque o GraphInstanceSet novamente, mas com uma descrição diferente e observe a descrição atualizada no conteúdo da resposta, indicando que a instância do grafo foi atualizada com êxito.
* Invoque GraphInstanceSet, mas altere o nome para "Sample-Graph-2" e observe o conteúdo de resposta. Observe que uma instância de grafo é criada (ou seja, o código de status é 201).

### <a name="invoke-graphinstanceactivate"></a>Invocar GraphInstanceActivate

Agora ative a instância do grafo, que inicia a transmissão de vídeo ao vivo por meio do módulo. Invoque o método direto GraphInstanceActivate com o conteúdo a seguir.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você deverá ver a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status 200 no conteúdo da resposta indica que a instância do grafo foi ativada com êxito.

### <a name="invoke-graphinstanceget"></a>Invocar GraphInstanceGet

Agora invoque o método direto GraphInstanceGet com o seguinte conteúdo:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Em poucos segundos, você deverá ver a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Observe o seguinte no conteúdo de resposta:

* O código de status é 200, indicando êxito.
* O estado é "Ativo", indicando que a instância do grafo agora está no estado "Ativo".

## <a name="observe-results"></a>Observar os resultados

A instância do grafo que criamos e ativamos acima usa o nó do processador de detecção de movimento para detectar o movimento no fluxo de vídeo ao vivo de entrada e envia eventos para o nó do coletor do Hub IoT. Esses eventos são retransmitidos para o Hub do IoT Edge, que agora podem ser observados. Para fazer isso, siga essas etapas.

1. Abra o painel do Explorer no Visual Studio Code e procure por Hub IoT do Azure, no canto inferior esquerdo.
2. Expanda o nó Dispositivos.
3. Clique com o botão direito do mouse em lva-sample-device e escolha a opção “Iniciar Monitoramento de Eventos Interno”.

![Iniciar o monitoramento de eventos do Hub IoT](./media/quickstarts/start-monitoring-iothub-events.png)

Você verá as seguintes mensagens na janela SAÍDA:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Observe o seguinte na mensagem acima

* A mensagem contém uma seção "body" e uma seção "applicationProperties". Para entender o que essas seções representam, leia o artigo [Criar e ler mensagem do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "subject" em applicationProperties faz referência ao nó no MediaGraph do qual a mensagem foi gerada. Nesse caso, a mensagem é originada do processador de detecção de movimento.
* "eventType" em applicationProperties indica que se trata de um evento de análise.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém os dados "timestamp" e "inferences".
* A seção "inferências" indica que o "tipo" é "movimento" e tem dados adicionais sobre o evento de "movimento".

Se você deixar o MediaGraph ser executado por algum tempo, verá a seguinte mensagem também na janela de Saída:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Observe o seguinte na mensagem acima

* "subject" em applicationProperties indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* "eventType" em applicationProperties indica que se trata de um evento de diagnóstico.
* "body" contém dados sobre o evento de diagnóstico. Nesse caso, o evento é MediaSessionEstablished, o que justifica esse corpo.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Invocar métodos diretos adicionais para limpeza

Agora invoque métodos diretos para desativar e excluir a instância de grafo (nessa ordem).

### <a name="invoke-graphinstancedeactivate"></a>Invocar GraphInstanceDeactivate

Invoque o método direto GraphInstanceDeactivate com o conteúdo a seguir.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você deverá ver a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status 200 indica que a instância do grafo foi desativada com êxito.

Experimente o mostrado a seguir como as próximas etapas.

* Invoque GraphInstanceGet conforme indicado nas seções anteriores e observe o valor de "state".

### <a name="invoke-graphinstancedelete"></a>Invocar GraphInstanceDelete

Invoque o método direto GraphInstanceDelete com o conteúdo a seguir

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você deverá ver a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status 200 na resposta indica que a instância do grafo foi excluída com êxito.

### <a name="invoke-graphtopologydelete"></a>Invocar GraphTopologyDelete

Invoque o método direto GraphTopologyDelete com o seguinte conteúdo:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, você deverá ver a seguinte resposta na janela SAÍDA:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status de 200 indica que a topologia de grafo foi excluída com êxito.

Experimente o mostrado a seguir como as próximas etapas.

* Invoque GraphTopologyList e observe que não há topologias de grafo no módulo.
* Invoque GraphInstanceList com o mesmo conteúdo que GraphTopologyList e observe que não há instâncias de grafo enumeradas.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não vai continuar usando este aplicativo, exclua os recursos que você criou neste guia de início rápido.

## <a name="next-steps"></a>Próximas etapas

* Saiba como gravar vídeo usando a Análise de Vídeo ao vivo no IoT Edge
* Saiba mais sobre as mensagens de diagnóstico.
