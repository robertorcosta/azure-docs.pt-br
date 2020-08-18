---
title: Detectar movimento e gravar vídeo em dispositivos de borda – Azure
description: Este início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera de IP (simulada), para detectar se qualquer movimento está presente; em caso afirmativo, registre um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 796def7cad3632dd50184bea751dc9f348569216
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067642"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Início Rápido: Detectar movimento e gravar vídeo em dispositivos de borda
 
Este início rápido mostra como usar a Análise Dinâmica de Vídeo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera IP (simulada). Ele mostra como detectar se há movimento e, caso houver, gravar um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda. Este início rápido usa uma VM do Azure como dispositivo do IoT Edge e um fluxo de vídeo ao vivo simulado. 

Este artigo é baseado no código de exemplo escrito em C#. Ele se baseia no início rápido [Detectar movimento e emitir eventos](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se não tiver uma.
* [Visual Studio Code](https://code.visualstudio.com/) com as extensões a seguir:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se ainda não tiver concluído o início rápido [Detectar movimento e emitir eventos](detect-motion-emit-events-quickstart.md), siga estas etapas:
     1. [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Configurar seu ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Gerar e implantar o manifesto de implantação do IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Preparar-se para monitorar eventos](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Ao instalar o Azure IoT Tools, talvez você receba um prompt para instalar o Docker. Fique à vontade para ignorá-lo.

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo
Quando você configura os recursos do Azure para este início rápido, um vídeo curto de um estacionamento é copiado para a VM Linux no Azure usada como o dispositivo do IoT Edge. Esse arquivo de vídeo será usado para simular uma transmissão ao vivo para este tutorial.

Abra um aplicativo como o [VLC media player](https://www.videolan.org/vlc/), selecione Ctrl+N e cole [este link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para o vídeo de estacionamento começar a ser reproduzido. Na marca de aproximadamente 5 segundos, um carro branco percorre o estacionamento.

Conclua as etapas abaixo para usar a Análise Dinâmica de Vídeo no IoT Edge para detectar o movimento do carro e gravar um clipe de vídeo começando na marca de cerca de 5 segundos.

## <a name="overview"></a>Visão geral

![visão geral](./media/quickstarts/overview-qs4.png)

O diagrama anterior mostra como os sinais fluem neste guia de início rápido. [Um módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de detecção de movimento](media-graph-concept.md#motion-detection-processor). A origem RTSP envia os mesmos quadros de vídeo para um nó do [processador de porta de sinal](media-graph-concept.md#signal-gate-processor), que permanece fechado até que seja disparado por um evento.

Quando o processador de detecção de movimento detecta o movimento no vídeo, ele envia um evento para o nó do processador de porta de sinal, disparando-o. A porta é aberta durante o tempo configurado, enviando quadros de vídeo para o nó do [coletor de arquivos](media-graph-concept.md#file-sink). O nó do coletor grava o vídeo como um arquivo MP4 no sistema de arquivos local de seu dispositivo de borda. O arquivo é salvo no local configurado.

Neste guia de início rápido, você vai:

1. Criar e implantar o grafo de mídia.
1. Interpretar os resultados.
1. Limpar os recursos.

## <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo
Como parte dos pré-requisitos deste início rápido, você baixou o código de exemplo para uma pasta. Siga estas etapas para examinar e editar o código de exemplo.

1. No Visual Studio Code, vá até *src/edge*. Você verá seu arquivo *.env* e alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são usadas variáveis para algumas propriedades. O arquivo *.env* inclui os valores dessas variáveis.
1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:
    * ***c2d-console-app.csproj*** – o arquivo de projeto para o Visual Studio Code.
    * ***operations.json*** – a lista de operações que você deseja que o programa execute.
    * ***Program.cs*** – o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](direct-methods.md). 
        * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos gerados pelo módulo na janela **SAÍDA**.
        * Invoca métodos diretos para limpar recursos.

1. Edite o arquivo *operations.json*:
    * Altere o link para a topologia do grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Em `GraphInstanceSet`, edite o nome da topologia de grafo para corresponder ao valor no link anterior:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Edite também a URL RTSP para apontar para o arquivo de vídeo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * Em `GraphTopologyDelete`, edite o nome:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Revisão – verificar o status dos módulos

Na etapa [Gerar e implantar o manifesto de implantação do IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest), no Visual Studio Code, expanda o nó **lva-sample-device** em **HUB IOT DO AZURE** (na seção inferior esquerda). Você deverá ver os seguintes módulos implantados:

* O módulo da Análise Dinâmica de Vídeo, chamado `lvaEdge`
* Um módulo chamado `rtspsim`, que simula um servidor RTSP que atua como a origem de um feed de vídeo ao vivo

  ![Módulos](./media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Se você estiver usando o próprio dispositivo de borda em vez de um provisionado pelo nosso script de instalação, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador**, para efetuar pull e armazenar o arquivo de vídeo de exemplo usado neste guia de início rápido:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

## <a name="review---prepare-for-monitoring-events"></a>Revisão – preparar-se para eventos de monitoramento
Verifique se você concluiu as etapas para [Preparar-se para monitorar eventos](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Iniciar Monitoramento de Ponto de Extremidade de Evento Interno](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

1. Inicie uma sessão de depuração pressionando F5. A janela **TERMINAL** imprime algumas mensagens.
1. O código *operations.json* chama os métodos diretos `GraphTopologyList` e `GraphInstanceList`. Se você limpou os recursos após os inícios rápidos anteriores, esse processo retornará listas vazias e, em seguida, pausará. Pressione Enter.

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

  A janela **TERMINAL** mostra o próximo conjunto de chamadas de método direto:  
  * Uma chamada para `GraphTopologySet` que usa o `topologyUrl` 
  * Uma chamada para `GraphInstanceSet` que usa o seguinte corpo:

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

  * Uma chamada para `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo
  * Uma segunda chamada para `GraphInstanceList` que mostra que a instância do grafo está em estado de execução  

3. A saída na janela **TERMINAL** pausa em `Press Enter to continue`. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá as mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.

1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:
     * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
     * Uma chamada para `GraphInstanceDelete` exclui a instância.
     * Uma chamada para `GraphTopologyDelete` exclui a topologia.
     * Uma chamada final para `GraphTopologyList` mostra que agora a lista está vazia.

## <a name="interpret-results"></a>Interpretar os resultados 
Quando você executa o grafo de mídia, os resultados do nó do processador de detecção de movimento passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela **SAÍDA** do Visual Studio Code contêm uma seção `body` e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso.

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

Na saída anterior: 

* A mensagem é um evento de diagnóstico, `MediaSessionEstablished`. Ela indica que o nó de origem RTSP (o assunto) estabeleceu uma conexão com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties`, `subject` faz referência ao nó na topologia do grafo do qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O valor `eventTime` é a hora em que o evento ocorreu.
* A seção `body` contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando o movimento é detectado, o nó do processador da porta do sinal é ativado e o nó do coletor de arquivos no grafo de mídia começa a gravar um arquivo MP4. O nó do coletor do arquivo envia um evento operacional. O `type` é definido como `motion` para indicar que se trata de um resultado do processador de detecção de movimento. O valor de `eventTime` é a hora, em UTC, em que o movimento ocorreu. Para obter mais informações sobre esse processo, confira a seção [Visão geral](#overview) deste início rápido.

Veja um exemplo dessa mensagem:

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

Na mensagem anterior: 

* Em `applicationProperties`, `subject` faz referência ao nó do grafo de mídia no qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó do coletor do arquivo.
* Em `applicationProperties`, `eventType` indica que este é um evento operacional.
* O valor `eventTime` é a hora em que o evento ocorreu. Essa hora é de 5 a 6 segundos após `MediaSessionEstablished`, e após o início do fluxo de vídeo. Isso corresponde à marca de 5 a 6 segundos quando [carro começou entrar](#review-the-sample-video) no estacionamento.
* A seção `body` contém dados sobre o evento operacional. Nesse caso, os dados são compostos por `outputType` e `outputLocation`.
* A variável `outputType` indica que essas informações são sobre o caminho do arquivo.
* O valor `outputLocation` é o local do arquivo MP4 no módulo de borda.

### <a name="recordingstopped-and-recordingavailable-events"></a>Eventos RecordingStopped e RecordingAvailable

Se você examinar as propriedades do nó processador da porta de sinal na [topologia do grafo](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), verá que os tempos de ativação foram definidos como 5 segundos. Portanto, cerca de 5 segundos após o evento `RecordingStarted` ser recebido, você tem:

* Um evento `RecordingStopped`, indicando que a gravação foi interrompida.
* Um evento `RecordingAvailable`, indicando que o arquivo MP4 agora pode ser usado para exibição.

Os dois eventos geralmente são emitidos em um intervalo de segundos entre si.

## <a name="play-the-mp4-clip"></a>Reproduzir o clipe MP4

Os arquivos MP4 são gravados em um diretório no dispositivo de borda que você configurou no arquivo *.env* usando a chave OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se você usou o valor padrão, os resultados devem estar na pasta */var/media/* .

Para reproduzir o clipe MP4:

1. Acesse o grupo de recursos, localize a VM e conecte-se usando o Azure Bastion.

    ![Resource group](./media/quickstarts/resource-group.png)
    
    ![VM](./media/quickstarts/virtual-machine.png)

1. Entre usando as credenciais que foram geradas quando você [configurou os recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. No prompt de comando, vá para o diretório relevante. A localização padrão é */var/media*. Você deve ver os arquivos MP4 no diretório.

    ![Saída](./media/quickstarts/samples-output.png) 

1. Use a [Cópia Segura (CSP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) para copiar os arquivos para o computador local. 
1. Execute os arquivos usando o [VLC media player](https://www.videolan.org/vlc/) ou qualquer outro player de MP4.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros inícios rápidos, mantenha os recursos criados. Caso contrário, no portal do Azure, navegue até seus grupos de recursos, selecione o grupo de recursos no qual você executou este início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

* Siga o guia de início rápido [Executar a Análise de Vídeo ao vivo com seu próprio modelo](use-your-model-quickstart.md), para aplicar a IA aos feeds de vídeo ao vivo.
* Examine os desafios adicionais para usuários avançados:

    * Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Veja as câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products). Procure dispositivos em conformidade com os perfis G, S ou T.
    * Use um dispositivo AMD64 ou x64 do Linux em vez de usar uma VM do Linux no Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Depois, siga as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md) para registrar o dispositivo no Hub IoT do Azure.
