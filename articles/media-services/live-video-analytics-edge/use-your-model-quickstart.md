---
title: Analisar vídeo ao vivo usando seu próprio modelo – Azure
description: Neste guia de início rápido, você aplicará a pesquisa visual computacional para analisar o feed de vídeo ao vivo de uma câmera IP (simulada).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261483"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Início Rápido: Analisar vídeo ao vivo usando seu próprio modelo

Este guia de início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera IP (simulada) aplicando um modelo de pesquisa visual computacional para detectar objetos. Um subconjunto dos quadros no feed de vídeo ao vivo é enviado para um serviço de inferência e os resultados correspondentes são enviados para o Hub do IoT Edge. Ele usa uma VM do Azure como dispositivo IoT Edge e uma transmissão de vídeo ao vivo simulada. Este artigo é baseado no código de exemplo escrito em C#.

Este artigo se baseia [neste](detect-motion-emit-events-quickstart.md) guia de início rápido. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) em seu computador com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado em seu sistema
* Se você não concluiu [este](detect-motion-emit-events-quickstart.md) guia de início rápido anteriormente, conclua as seguintes etapas:
     * [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Ao instalar o Azure IoT Tools, você receberá uma solicitação para instalar o Docker. Fique à vontade para ignorá-la.

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo
Como parte das etapas acima para configurar os recursos do Azure, um vídeo (curto) de um tráfego de rodovia será copiado para a VM do Linux no Azure que está sendo usada como o dispositivo IoT Edge. Esse arquivo de vídeo será usado para simular uma transmissão ao vivo para este tutorial.

Você pode usar um aplicativo como [VLC Player](https://www.videolan.org/vlc/), iniciá-lo, pressionar Control + N e colar [este](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) link para a reprodução do vídeo começar. Você verá que a sequência de imagens é de tráfego em uma rodovia, com muitos veículos se movendo.

Ao concluir as etapas abaixo, você terá usado a Análise de Vídeo ao vivo no IoT Edge para detectar objetos como veículos, pessoas etc. e publicar eventos de inferência associados no Hub do IoT Edge.

## <a name="overview"></a>Visão geral

![Visão geral](./media/quickstarts/overview-qs5.png)

O diagrama acima mostra como os sinais fluem neste guia de início rápido. Um módulo de borda (detalhado [aqui](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simula uma câmera IP que hospeda um servidor RTSP. Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de filtro de taxa de quadros](media-graph-concept.md#frame-rate-filter-processor). Esse processador limita a taxa de quadros do fluxo de vídeo que alcança o nó do [processador de extensão HTTP](media-graph-concept.md#http-extension-processor). 

O nó de extensão HTTP desempenha a função de um proxy, convertendo os quadros de vídeo para o tipo de imagem especificado e retransmitindo a imagem por REST para outro módulo do Edge que executa um modelo de IA subjacente a um ponto de extremidade HTTP. Neste exemplo, esse módulo do Edge é criado usando o modelo [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), que é capaz de detectar muitos tipos de objetos. O nó de processador de extensão HTTP reúne os resultados da detecção e publica eventos no nó de [coletor do Hub IoT](media-graph-concept.md#iot-hub-message-sink ) que, em seguida, envia esses eventos para o [Hub do IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Neste guia de início rápido, você vai:

1. Criar e implantar o grafo de mídia.
1. Interpretar os resultados.
1. Limpe os recursos.



## <a name="create-and-deploy-the-media-graph"></a>Criar e implantar o grafo de mídia
    
### <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo

Como parte dos pré-requisitos, você baixou o código de exemplo para uma pasta. Inicie o Visual Studio Code e abra a pasta.

1. No Visual Studio Code, navegue até "src/edge". Você verá o arquivo .env criado junto com alguns arquivos do modelo de implantação.

    * O modelo de implantação refere-se ao manifesto de implantação para o dispositivo de borda com alguns valores de espaço reservado. O arquivo .env tem os valores para essas variáveis.
1. Em seguida, navegue até a pasta "src/cloud-to-device-console-app". Aqui você verá o arquivo appsettings.json criado junto com alguns outros arquivos:

    * c2d-console-app.csproj – esse é o arquivo de projeto para o Visual Studio Code.
    * operations.json – esse arquivo listará as diferentes operações que você gostaria que o programa executasse.
    * Program.cs – este é o código de exemplo do programa, que faz o seguinte:

        * Carrega as configurações do aplicativo.
        *  Invoca métodos diretos expostos pela Análise de Vídeo ao vivo no módulo do IoT Edge. Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando [métodos diretos](direct-methods.md) 
        * Pausa para você examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela SAÍDA
        * Invoca métodos diretos para limpar recursos   


1. Fazer as edições a seguir no arquivo operations.json
    * Altere o link para a topologia do grafo: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia de grafo para corresponder ao valor no link acima `"topologyName" : "InferencingWithHttpExtension"`
    * Em GraphTopologyDelete, edite o nome `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

1. Clique com o botão direito do mouse no arquivo "src/edge/deployment.yolov3.template.json" e clique em Gerar o Manifesto de Implantação do IoT Edge.

    ![Gerar um Manifesto de Implantação do IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Isso deve criar um arquivo de manifesto na pasta src/edge/config chamada "deployment.yolov3.amd64.json".
1. Se você tiver concluído anteriormente o [guia de início rápido](detect-motion-emit-events-quickstart.md), ignore esta etapa. Caso contrário, defina a cadeia de conexão IoTHub clicando no ícone "mais ações" ao lado do painel HUB IOT DO AZURE no canto inferior esquerdo. Você pode copiar a cadeia de caracteres do arquivo appsettings.json. (Aqui está outra abordagem recomendada para garantir que você tenha o Hub IoT adequado configurado no Visual Studio Code por meio do [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![Cadeia de conexão IoTHub](./media/quickstarts/set-iotconnection-string.png)
1. Em seguida, clique com o botão direito do mouse em "src/edge/config/deployment.yolov3.amd64.json" e clique em "Criar implantação para dispositivo único". 

    ![Criar implantação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, você será solicitado a selecionar um dispositivo de Hub IoT. Selecione lva-sample-device na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure na seção inferior esquerda e você deverá ter o dispositivo de borda com os seguintes módulos implantados:

    1. O módulo Análise de Vídeo ao vivo, chamado de "lvaEdge".
    1. Um módulo chamado "rtspsim" que simula um servidor RTSP, agindo como a origem de um feed de vídeo ao vivo.
    1. Um módulo chamado "yolov3" que, como o nome sugere, é o modelo de detecção de objetos YOLOv3 que aplica a pesquisa visual computacional às imagens e retorna várias classes de tipos de objetos.
 
        ![Modelo de detecção de objetos YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Clique com o botão direito do mouse no dispositivo de Análise de Vídeo ao vivo e clique em "Iniciar Monitoramento de Ponto de Extremidade de Evento Interno". Essa etapa é necessária para monitorar os eventos do Hub IoT e vê-los na janela de Saída do Visual Studio Code. 

![Começar a monitorar](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Executar o programa de exemplo

1. Inicie uma sessão de depuração (pressione F5). Você começará a ver algumas mensagens impressas na janela TERMINAL.
1. O operations.json começa com chamadas aos métodos diretos GraphTopologyList e GraphInstanceList. Se você tiver limpado os recursos após os guias de início rápido anteriores, isso retornará listas vazias e, em seguida, pausará para que você pressione Enter
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. Quando você pressiona a tecla "Enter" na janela TERMINAL, o próximo conjunto de chamadas de método direto é feito
     * Uma chamada para GraphTopologySet usando a topologyUrl acima
     * Uma chamada para GraphInstanceSet usando o corpo a seguir
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * Uma chamada para GraphInstanceActivate para iniciar a instância do grafo e iniciar a transmissão do vídeo
     * Uma segunda chamada para GraphInstanceList para mostrar que a instância de grafo está realmente no estado de execução
1. A saída na janela TERMINAL será pausada agora com o prompt "Pressione Enter para continuar". Não pressione "Enter" no momento. Você pode rolar para cima para ver o conteúdo da resposta JSON para os métodos diretos que você invocou
1. Se agora você alternar para a janela SAÍDA no Visual Studio Code, verá as mensagens que estão sendo enviadas para o Hub IoT, pela Análise de Vídeo ao vivo no módulo do IoT Edge.
     * Estas mensagens são discutidas na seção abaixo
1. O grafo de mídia continuará sendo executado e imprimirá os resultados – o simulador RTSP continuará fazendo loop do vídeo de origem. Para parar o grafo de mídia, volte para a janela TERMINAL e pressione "Enter". A próxima série de chamadas é feita para limpar os recursos:
     * Uma chamada para GraphInstanceDeactivate para desativar a instância de grafo
     * Uma chamada para GraphInstanceDelete para excluir a instância
     * Uma chamada para GraphTopologyDelete para excluir a topologia
     * Uma chamada final para GraphTopologyList para mostrar que a lista está vazia agora

## <a name="interpret-results"></a>Interpretar os resultados

Quando você executa o grafo de mídia, os resultados do nó do processador de extensão HTTP são enviados por meio do nó do coletor do Hub IoT para o Hub IoT. As mensagens que você vê na janela SAÍDA do Visual Studio Code contêm uma seção "body" e uma seção "applicationProperties". Para entender o que essas seções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo Análise de Vídeo ao vivo. 



### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se for bem-sucedido, ele imprimirá esse evento. O tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Observe o seguinte na mensagem acima:
* A mensagem é um Evento de diagnóstico, MediaSessionEstablished, que indica que o nó de origem RTSP (o assunto) foi capaz de estabelecer uma conexão com o simulador RTSP e começar a receber um feed ao vivo (simulado).
* "subject" em applicationProperties indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* "eventType" em applicationProperties indica que se trata de um evento de diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do módulo yolov3 e os emite por meio do nó do coletor do Hub IoT como eventos de inferência. Nesses eventos, o tipo é definido como "entidade" para indicar que se trata de uma entidade como carro ou caminhão, e o eventTime informa em que horário (UTC) o objeto foi detectado. Abaixo está um exemplo em que dois carros foram detectados com níveis de confiança diferentes no mesmo quadro de vídeo.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Observe o seguinte nas mensagens acima:

* "subject" em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. 
* "eventType" em applicationProperties indica que se trata de um evento de análise.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém os dados de "inferences".
* A seção "inferences" indica que o "type" é "entity" e tem dados adicionais sobre a "entity".

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros guias de início rápido, deve ater-se aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este guia de início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://www.onvif.org/conformant-products/) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Você pode seguir as instruções em [Instalar o runtime do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e seguir as instruções no guia de início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registrar o dispositivo no Hub IoT do Azure.

