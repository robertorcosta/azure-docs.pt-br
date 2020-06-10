---
title: Detectar movimento e gravar vídeo em dispositivos de borda – Azure
description: Este início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera de IP (simulada), para detectar se qualquer movimento está presente; em caso afirmativo, registre um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261681"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Início Rápido: Detectar movimento e gravar vídeo em dispositivos de borda
 
Este início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera de IP (simulada), para detectar se qualquer movimento está presente; em caso afirmativo, registre um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda. Ele usa uma VM do Azure como dispositivo IoT Edge e uma transmissão de vídeo ao vivo simulada. Este artigo é baseado no código de exemplo escrito em C#.

Este artigo se baseia [neste](detect-motion-emit-events-quickstart.md) guia de início rápido. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) em seu computador com as seguintes extensões:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado em seu sistema
* Se você não concluiu [este](detect-motion-emit-events-quickstart.md) guia de início rápido anteriormente, conclua as seguintes etapas:
     * [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Configurar o ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Gerar e implantar o manifesto de implantação do IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Preparar-se para eventos de monitoramento](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Ao instalar o Azure IoT Tools, você receberá uma solicitação para instalar o docker. Fique à vontade para ignorá-la.

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo
Como parte das etapas acima para configurar os recursos do Azure, um vídeo (curto) de um estacionamento será copiado para a VM Linux no Azure que está sendo usada como o dispositivo IoT Edge. Esse arquivo de vídeo será usado para simular uma transmissão ao vivo para este tutorial.

Você pode usar um aplicativo como [VLC Player](https://www.videolan.org/vlc/), iniciá-lo, pressionar Control + N e colar [este](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) link para o vídeo de estacionamento iniciar a reprodução. Na marca de aproximadamente 5 segundos, um carro branco percorre o estacionamento.

Ao concluir as etapas abaixo, você terá usado a Análise de Vídeo ao vivo no IoT Edge para detectar esse movimento do carro e gravar um clipe de vídeo começando na marca de cerca de 5 segundos.

## <a name="overview"></a>Visão geral

![visão geral](./media/quickstarts/overview-qs4.png)

O diagrama acima mostra como os sinais fluem neste guia de início rápido. Um módulo de borda (detalhado [aqui](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simula uma câmera IP que hospeda um servidor RTSP. Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de detecção de movimento](media-graph-concept.md#motion-detection-processor). A origem RTSP envia os mesmos quadros de vídeo para um nó do [processador de porta de sinal](media-graph-concept.md#signal-gate-processor), que permanece fechado até que seja disparado por um evento.

Quando o processador de detecção de movimento determina esse movimento está presente no vídeo, ele envia um evento para o nó do processador de porta de sinal, disparando-o. A porta é aberta durante o tempo configurado, enviando quadros de vídeo para o nó do [coletor de arquivos](media-graph-concept.md#file-sink). O nó do coletor grava o vídeo como um arquivo MP4 no sistema de arquivos local do seu dispositivo de borda, no local configurado.

Neste guia de início rápido, você vai:

1. Criar e implantar o grafo de mídia
1. Interpretar os resultados
1. Limpar os recursos

## <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo
Como parte dos pré-requisitos, você baixou o código de exemplo para uma pasta. Inicie o Visual Studio Code e abra a pasta.

1. No Visual Studio Code, navegue até "src/edge". Você verá o arquivo .env criado junto com alguns arquivos do modelo de implantação
    * O modelo de implantação refere-se ao manifesto de implantação para o dispositivo de borda com alguns valores de espaço reservado. O arquivo .env tem os valores para essas variáveis.
1. Em seguida, navegue até a pasta "src/cloud-to-device-console-app". Aqui você verá o arquivo appsettings.json criado junto com alguns outros arquivos:
    * c2d-console-app.csproj – este é o arquivo de projeto para o Visual Studio Code
    * operations.json – esse arquivo listará as diferentes operações que você gostaria que o programa executasse
    * Program.cs – este é o código de exemplo do programa, que faz o seguinte:

        * Carrega as configurações do aplicativo
        * Invoca métodos diretos expostos pela Análise de Vídeo ao vivo no módulo do IoT Edge. Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando [métodos diretos](direct-methods.md) 
        * Pausa para você examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela SAÍDA
        * Invoca métodos diretos para limpar recursos   

1. Fazer as edições a seguir no arquivo operations.json
    * Altere o link para a topologia do grafo: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia de grafo para corresponder ao valor no link acima `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edite também a URL RTSP para apontar para o arquivo de vídeo desejado `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Em GraphTopologyDelete, edite o nome `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Examinar – verificar o status dos módulos
Na etapa [Gerar e implantar o manifesto de implantação do IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest), no Visual Studio Code; se você expandir o nó “lva-sample-device” em HUB IOT DO AZURE (na seção inferior esquerda), deverá ver os módulos a seguir implantados

    1. O módulo Análise de Vídeo ao vivo, chamado de “lvaEdge”
    1. Um módulo chamado "rtspsim" que simula um servidor RTSP, agindo como a origem de um feed de vídeo ao vivo

        ![Módulos](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Examinar – preparar-se para eventos de monitoramento
Verifique se você concluiu as etapas para [Preparar-se para eventos de monitoramento](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Iniciar Monitoramento de Ponto de Extremidade de Evento Interno](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

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
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
Quando você executa o grafo de mídia, os resultados do nó do processador de detecção de movimento são enviados por meio do nó do coletor do Hub IoT para o Hub IoT. As mensagens que você vê na janela SAÍDA do Visual Studio Code contêm uma seção "body" e uma seção "applicationProperties". Para entender o que essas seções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo Análise de Vídeo ao vivo.

## <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se for bem-sucedido, ele imprimirá este evento:

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

* A mensagem é um Evento de diagnóstico, MediaSessionEstablished, que indica que o nó de origem RTSP (o assunto) foi capaz de estabelecer uma conexão com o simulador RTSP e começar a receber um feed ao vivo (simulado).
* O "assunto" em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* "eventType" em applicationProperties indica que se trata de um Evento de diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="recording-started-event"></a>Evento Gravação Iniciada

Conforme explicado [aqui](#overview), quando o movimento é detectado, o nó do processador da porta do sinal é ativado e o nó do coletor de arquivos no grafo de mídia começa a gravar um arquivo MP4. O nó do coletor do arquivo envia um Evento operacional. O tipo é definido como “movimento” para indicar que é resultado do Processador de Detecção de Movimento, e o eventTime informa você em que horário (UTC) o movimento ocorreu. Veja um exemplo:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

* "subject" em applicationProperties faz referência ao nó no grafo de mídia do qual a mensagem foi gerada. Nesse caso, a mensagem é originada no nó do coletor do arquivo.
* "eventType" em applicationProperties indica que se trata de um Evento operacional.
* "eventTime" indica a hora em que o evento ocorreu. Observe que isso ocorre de 5 a 6 segundos após o MediaSessionEstablished e o vídeo começa a fluir. Isso corresponde à marca de 5 a 6 segundos quando [carro começou a percorrer](#review-the-sample-video) o estacionamento
* "body" contém dados sobre o evento operacional que, nesse caso, são os dados "outputType" e "outputLocation".
* "outputType" indica que essas informações são sobre o caminho do arquivo
* "outputLocation" fornece o local do arquivo MP4, de dentro do módulo de borda

## <a name="recording-stopped-and-available-events"></a>Gravar eventos Interrompidos e Disponíveis

Se você examinar as propriedades do nó processador da porta de sinal na [topologia do grafo](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), verá que os tempos de ativação foram definidos como 5 segundos. Portanto, cerca de 5 segundos após o evento RecordingStarted ser recebido, você receberá um
* Evento RecordingStopped, indicando que a gravação foi interrompida
* Evento RecordingAvailable, indicando que o arquivo MP4 agora pode ser usado para exibição

Os dois eventos geralmente são emitidos com segundos entre si.

### <a name="playing-back-the-mp4-clip"></a>Reproduzir o clipe MP4

1. Os arquivos MP4 são gravados em um diretório no dispositivo de borda que você configurou no arquivo .env por meio desta chave – OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se você tiver mantido o valor padrão, os resultados deverão estar em /home/lvaadmin/samples/output/
1. Acesse o grupo de recursos, localize a VM e conecte-se usando o Bastion

    ![Resource group](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Após entrar (usando credenciais que foram geradas durante [esta](detect-motion-emit-events-quickstart.md#set-up-azure-resources) etapa), no prompt de comando, acesse o respectivo diretório (padrão: /home/lvaadmin/samples/output) e você deverá ver os arquivos MP4 lá. Você pode fazer [scp dos arquivos](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) em seu computador local e reproduzi-los de volta por meio do [VLC Player](https://www.videolan.org/vlc/) ou de qualquer outro player MP4.

    ![Saída](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros guias de início rápido, deve ater-se aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este guia de início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

* Execute o guia de início rápido [Executar a Análise de Vídeo ao vivo com seu próprio modelo](use-your-model-quickstart.md), que mostra como aplicar a IA aos feeds de vídeo ao vivo.
* Examine os desafios adicionais para usuários avançados:

    * Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://en.wikipedia.org/wiki/IP_camera) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
    * Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Você pode seguir as instruções em [Instalar o runtime do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e seguir as instruções no guia de início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registrar o dispositivo no Hub IoT do Azure.
