---
title: Tutorial de gravação de vídeo baseada em eventos para a nuvem e reprodução com origem na nuvem – Azure
description: Neste tutorial, você vai aprender a usar a Análise de Vídeo ao vivo no IoT Edge para executar uma gravação de vídeo baseada em eventos para a nuvem e reprodução com origem na nuvem.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7ff8502688e2b507b8a576c177948f29c2248be4
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456597"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução com origem na nuvem

Neste tutorial, você vai aprender a usar a Análise de Vídeo ao vivo no IoT Edge para registrar seletivamente partes de uma fonte de vídeo ao vivo nos Serviços de Mídia na nuvem. Esse caso de uso é chamado de [EVR (gravação de vídeo baseada em eventos)](event-based-video-recording-concept.md) neste tutorial. Para fazer isso, você vai usar um modelo de IA de detecção de objetos para procurar objetos no vídeo e gravar clipes de vídeo somente quando um determinado tipo de objeto for detectado. Você também vai aprender a reproduzir os clipes de vídeo gravados usando os Serviços de Mídia. Isso é útil para uma série de cenários em que há a necessidade de manter um arquivo de clipes de vídeo de interesse.

> [!div class="checklist"]
> * Configurar os recursos relevantes
> * Examinar o código que executa a EVR
> * Execute o código de exemplo
> * Examinar os resultados e exibir o vídeo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

É recomendável que você leia as páginas da documentação a seguir

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md) 
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: desenvolvendo um módulo do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Como editar deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Seção sobre [como declarar rotas no manifesto de implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são os mostrados a seguir

* [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com a extensão do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e a extensão do [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Pode ser solicitado que você instale o Docker. Você pode ignorar esse aviso.
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) instalado no computador de desenvolvimento.
* Conclua o [script de instalação de recursos da Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [Configure o ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Ao final das etapas acima, você terá determinados recursos do Azure implantados na assinatura do Azure, incluindo:

* Hub IoT
* Conta de armazenamento
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

## <a name="concepts"></a>Conceitos

A EVR (Gravação de vídeo baseada em eventos) se refere ao processo de gravação de vídeo acionado por um evento. Esse evento pode ser gerado do processamento do próprio sinal do vídeo (por exemplo, ao detectar um objeto específico em movimento no vídeo) ou de uma fonte independente (por exemplo, a abertura de uma porta). Opcionalmente, você pode disparar a gravação somente quando um serviço de inferência detectar que ocorreu um evento específico.  Neste tutorial, você vai usar um vídeo de veículos se movendo em uma rodovia e gravar clipes de vídeo sempre que um caminhão for detectado.

![Grafo de mídia](./media/event-based-video-recording-tutorial/overview.png)

O diagrama acima é uma representação em ilustração de um [grafo de mídia](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Há quatro módulos do IoT Edge envolvidos:

* Módulo Análise de Vídeo ao vivo no IoT Edge.
* Um módulo do Edge executando um modelo de IA por trás de um ponto de extremidade HTTP. Este módulo de IA usa o modelo [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), que é capaz de detectar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos (chamado de Contador de Objetos no diagrama acima) que você cai criar e implantar neste tutorial.
* Um [módulo simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmera RTSP.
    
    Como mostra o diagrama, você vai usar um nó de [origem RTSP](media-graph-concept.md#rtsp-source) no grafo de mídia para capturar o vídeo ao vivo simulado (de tráfego em uma rodovia) e enviá-lo para dois caminhos.

* O primeiro caminho é para um nó do [processador de filtro de taxa de quadros](media-graph-concept.md#frame-rate-filter-processor), que gera quadros de vídeo à taxa de quadros especificada (reduzida). Esses quadros de vídeo são enviados para um nó de extensão HTTP, que, em seguida, retransmite os quadros (como imagens) para o módulo de IA (YOLO v3, que é um detector de objetos) e recebe resultados, que serão os objetos (veículos do tráfego) detectados pelo modelo. Em seguida, o nó de extensão HTTP publica os resultados por meio do nó coletor de mensagens do Hub IoT no Hub do IoT Edge.
* O módulo contador de objetos é configurado para receber mensagens do Hub do IoT Edge, que incluem os resultados de detecção de objetos (veículos do tráfego). Ele verifica essas mensagens procurando por objetos de um determinado tipo (configurados por meio de uma definição). Quando tal objeto é encontrado, esse módulo envia uma mensagem ao Hub do IoT Edge. Essas mensagens de "objeto encontrado" são roteadas para o nó de origem do Hub IoT do grafo de mídia. Ao receber tal mensagem, o nó de origem do Hub IoT no grafo de mídia aciona o nó do [processador de portão de sinal](media-graph-concept.md#signal-gate-processor), fazendo com que este fique aberto por um período configurado. O vídeo flui pelo portão para o nó do coletor de ativos durante esse período. Então, essa parte da transmissão ao vivo é registrada por meio do nó do [coletor de ativos](media-graph-concept.md#asset-sink) em um [ativo](terminology.md#asset) da conta do Serviço de Mídia do Azure.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Antes de começar, verifique se você concluiu o 3º marcador em [Pré-requisitos](#prerequisites). Quando o script de instalação do recurso for concluído, clique nas chaves para expor a estrutura de pastas. Você verá alguns arquivos criados no diretório ~/clouddrive/lva-sample.

![Configurações do aplicativo](./media/quickstarts/clouddrive.png)

Os arquivos de interesse neste tutorial são:

* ~/clouddrive/lva-sample/edge-deployment/.env – contém propriedades que o Visual Studio Code usa para implantar módulos em um dispositivo de borda.
* ~/clouddrive/lva-sample/appsetting.json – usado pelo Visual Studio Code para executar o código de exemplo.

Você precisará desses arquivos para as etapas abaixo.

1. Clone o repositório aqui: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Inicie o Visual Studio Code e abra a pasta para a qual você baixou o repositório.
1. No Visual Studio Code, navegue até a pasta "src/cloud-to-device-console-app" e crie um arquivo chamado "appsettings.json". Esse arquivo vai conter as configurações necessárias para executar o programa.
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/appsettings.json. O texto deve ter esta aparência:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    A cadeia de conexão do Hub IoT permite que você use o Visual Studio Code para enviar comandos para os módulos do Edge por meio do Hub IoT do Azure.
    
1. Em seguida, navegue até a pasta "src/edge" e crie um arquivo chamado ".env".
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/.env. O texto deve ter esta aparência:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Examine o arquivo de modelo 

Na etapa anterior, você vai ter iniciado o Visual Studio Code e aberto a pasta que contém o código de exemplo.

No Visual Studio Code, navegue até "src/edge". Você verá o arquivo .env criado, bem como alguns arquivos do modelo de implantação. Este modelo define quais módulos de borda serão implantados no dispositivo de borda (a VM Linux do Azure). O arquivo .env contém valores para as variáveis usadas nesses modelos, como as credenciais do Serviço de Mídia.

Abra "src/edge/deployment.objectCounter.template.json". Observe que há quatro entradas na seção "modules", correspondentes aos itens listados acima (na seção Conceitos):

* lvaEdge – essa é a Análise de Vídeo ao vivo no módulo o IoT Edge
* yolov3 – é o módulo de IA criado usando o modelo YOLO v3
* rtspsim – esse é o simulador RTSP
* objectCounter: é o módulo que procura por objetos específicos nos resultados do yolov3

Para o módulo objectCounter, confira a cadeia de caracteres (${MODULES.objectCounter}) usada para o valor "Image" – isso se baseia no [tutorial](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) sobre o desenvolvimento de um módulo do IoT Edge. O Visual Studio Code reconhecerá automaticamente que o código para o módulo do Contador de Objeto está em "src/edge/modules/objectCounter". 

Leia [esta](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) seção sobre como declarar rotas no manifesto de implantação do IoT Edge e, em seguida, examine as rotas do arquivo JSON do modelo. Observe como:

* O LVAToObjectCounter é usado para enviar eventos específicos para um ponto de extremidade específico no módulo objectCounter.
* O ObjectCounterToLVA é usado para enviar um evento de gatilho para um ponto de extremidade específico (que deve ser o nó de origem do Hub IoT) no módulo lvaEdge.
* O objectCounterToIoTHub é usado como ferramenta de depuração, para ajudar a enxergar a saída do objectCounter ao executar este tutorial.

> [!NOTE]
> Verifique as propriedades desejadas do módulo objectCounter, que são configuradas para procurar por objetos marcados como "caminhão", com um nível de confiança de pelo menos 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge 

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto desse tipo no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.

Usando o Visual Studio Code, siga [essas](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) instruções para fazer logon no Docker e "Compilar e efetuar push da solução do IoT Edge", mas use src/edge/deployment.objectCounter.template.json para esta etapa.

![Compilar e efetuar push da solução do IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Isso vai criar o módulo objectCounter para contagem de objetos e vai efetuar push da imagem para o ACR (Registro de contêiner do Azure).

* Verifique se há as variáveis de ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no arquivo .env.

A etapa acima vai criar o manifesto de implantação do IoT Edge em src/edge/config/deployment.objectCounter.amd64.json. Clique com o botão direito do mouse nesse arquivo e clique em "Criar implantação para dispositivo único".

![Criar implantação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)

Se este for seu primeiro tutorial com a Análise de Vídeo ao vivo no IoT Edge, o Visual Studio Code solicitará que você insira a cadeia de conexão do Hub IoT. Você pode copiá-la do arquivo appsettings.json.

Em seguida, o Visual Studio Code solicitará que você selecione um dispositivo do Hub IoT. Selecione seu dispositivo do IoT Edge (deve ser "lva-sample-device").

Nesta fase, a implantação dos módulos de borda em seu dispositivo do IoT Edge já foi iniciada.
Em cerca de 30 segundos, atualize o Hub IoT do Azure na seção inferior esquerda do Visual Studio Code e vai ver que há quatro módulos implantados (lembre-se novamente dos nomes: lvaEdge, rtspsim, yolov3 e objectCounter).

![4 módulos implantados](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Para ver os eventos do módulo do contador de objetos e do módulo de Análise de Vídeo ao vivo no IoT Edge, execute as seguintes etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure por Hub IoT do Azure, no canto inferior esquerdo.
1. Expanda o nó Dispositivos.
1. Clique com o botão direito do mouse em lva-sample-device e escolha a opção **Iniciar Monitoramento de evento interno**.

![Iniciar monitoramento de ponto de extremidade de evento interno](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Execute o programa

1. Visual Studio Code, navegue até "src/cloud-to-device-console-app/operations.json"

1. Sob o nó GraphTopologySet, edite o seguinte:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Em seguida, sob os nós GraphInstanceSet e GraphTopologyDelete, edite,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Inicie uma sessão de depuração (pressione F5). Você começará a ver algumas mensagens impressas na janela TERMINAL.

1. O operations.json começa com chamadas a GraphTopologyList e GraphInstanceList. Se você tiver limpado os recursos após os guias de início rápido ou tutoriais anteriores, isso retornará listas vazias e, em seguida, pausará para que você pressione Enter, conforme mostrado abaixo:

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
    1. Quando você pressiona a tecla "Enter" na janela TERMINAL, o próximo conjunto de chamadas de método direto é feito.
     * Uma chamada para GraphTopologySet usando a topologyUrl acima.
     * Uma chamada para GraphInstanceSet usando o corpo a seguir.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. A saída na janela TERMINAL será pausada agora com o prompt "Pressione Enter para continuar". Não pressione "Enter" no momento. Você pode rolar para cima para ver o conteúdo da resposta JSON para os métodos diretos que você invocou.

1. Se agora você alternar para a janela SAÍDA no Visual Studio Code, verá as mensagens que estão sendo enviadas para o Hub IoT, pela Análise de Vídeo ao vivo no módulo do IoT Edge.

     * Algumas mensagens são discutidas na seção a seguir.
     
1. A instância do grafo vai continuar sendo executada e gravando o vídeo. O simulador RTSP, por sua vez, vai continuar executando em loop o vídeo de origem. Examine as mensagens conforme discutido na seção abaixo e, em seguida, para interromper a instância, volte para a janela TERMINAL e pressione "Enter". A próxima série de chamadas é feita para limpar os recursos:

     * Uma chamada para GraphInstanceDeactivate para desativar a instância de grafo
     * Uma chamada para GraphInstanceDelete para excluir a instância
     * Uma chamada para GraphTopologyDelete para excluir a topologia
     * Uma chamada final para GraphTopologyList para mostrar que a lista está vazia agora

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando você executa o grafo de mídia, o módulo de Análise de Vídeo ao vivo no IoT Edge envia determinados eventos de diagnóstico e operacionais para o Hub do IoT Edge. Esses eventos são as mensagens que você vê na janela SAÍDA do Visual Studio Code, as quais contêm uma seção "body" e uma seção "applicationProperties". Para entender o que essas seções representam, confira [Criar e ler as mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nas mensagens abaixo, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo Análise de Vídeo ao vivo.

## <a name="diagnostic-events"></a>Eventos de diagnóstico

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished 

Quando um grafo de mídia é instanciado, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner do simulador RTSP. Se for bem-sucedido, ele imprimirá esse evento. Observe que o tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* A mensagem é um Evento de diagnóstico, MediaSessionEstablished, que indica que o nó de origem RTSP (o assunto) foi capaz de estabelecer uma conexão com o simulador RTSP e começar a receber um feed ao vivo (simulado).

* O "assunto" em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.

* "eventType" em applicationProperties indica que se trata de um evento de diagnóstico.

* O "eventTime" indica a hora em que o evento ocorreu, que é a hora em que o vídeo do tráfego (arquivo MKV) começou a chegar ao módulo como uma transmissão ao vivo.

* "body" contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Eventos operacionais

Depois que o grafo de mídia for executado por um tempo, cedo ou tarde você receberá um evento do módulo do Contador de Objetos. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

O applicationProperties contém o eventTime, que é a hora em que o módulo do Contador de Objetos observou que os resultados do módulo YOLO v3 continham objetos de interesse (caminhões).

Você poderá ver mais desses eventos surgirem à medida que outros caminhões forem detectados no vídeo.

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quase imediatamente após o Contador de Objeto enviar o evento, você verá um evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

O "assunto" em applicationProperties faz referência ao nó do coletor de ativos no grafo, que gerou essa mensagem. O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado. Anote esse valor.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Quando o nó do coletor de ativos houver carregado o vídeo no ativo, ele emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

O evento indica que dados suficientes foram escritos no Ativo para que os players/clientes iniciassem a reprodução do vídeo. O "subject" em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem. O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Se você examinar as configurações de ativação (maximumActivationTime) para o nó do processador do portão do sinal na [topologia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), você vai ver que o portão está configurado para fechar depois que 30 segundos de vídeo houver sido enviado. Aproximadamente 30 segundos após o evento RecordingStarted, você deverá ver um evento do tipo Microsoft.Media.Graph.Operational.RecordingStopped, indicando que o nó do coletor de ativos parou de gravar vídeo no ativo.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Esse evento indica que a gravação foi interrompida. O "subject" em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem. O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo dos Serviços de Mídia  

Você vai poder examinar o ativo de Serviços de Mídia criado pelo grafo de mídia fazendo logon no portal do Azure e exibindo o vídeo.

1. Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.
1. Localize sua conta dos Serviços de Mídia entre os recursos que você tem em sua assinatura e abra a folha da conta
1. Clique em Ativos na listagem de Serviços de Mídia.

    ![Ativos](./media/continuous-video-recording-tutorial/assets.png)
1. Você encontrará um ativo listado com o nome sampleAssetFromEVR-LVAEdge-{DateTime} – esse é o nome fornecido na propriedade outputLocation do evento RecordingStarted. O assetNamePattern na topologia determina como esse nome foi gerado.
1. Clique no Ativo.
1. Na página detalhes do ativo, clique em **Criar** abaixo da caixa de texto da URL de streaming.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que é aberto, aceite as opções padrão e clique em "Adicionar". Para obter mais informações, confira [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Verifique se o [ponto de extremidade de streaming está em execução](../latest/streaming-endpoint-concept.md).
1. O player deverá carregar o vídeo e você deverá conseguir clicar em **Reproduzir** para exibi-lo.

> [!NOTE]
> Como a origem do vídeo era um contêiner simulando um feed de câmera, os carimbos de data/hora do vídeo estão relacionados aos momentos em que você ativou a instância do grafo e quando a desativou. Se você usar os controles de reprodução incorporados ao tutorial de [Reprodução de gravações de vários dias](playback-multi-day-recordings-tutorial.md), você poderá ver os carimbos de data/hora do vídeo exibidos na tela.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros tutoriais, deve se ater aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://www.onvif.org/conformant-products/) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Você pode seguir as instruções em [Instalar o runtime do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e seguir as instruções no guia de início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registrar o dispositivo no Hub IoT do Azure.
