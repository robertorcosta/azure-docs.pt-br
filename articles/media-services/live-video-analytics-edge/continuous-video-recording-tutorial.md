---
title: Tutorial de gravação de vídeo contínua para nuvem e reprodução com origem na nuvem – Azure
description: Neste tutorial, você aprenderá a usar a Análise de Vídeo ao vivo no IoT Edge para gravar continuamente o vídeo para a nuvem e transmitir qualquer parte desse vídeo usando os Serviços de Mídia do Azure.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259982"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Tutorial: Gravação de vídeo contínua para nuvem e reprodução com origem na nuvem  

Neste tutorial, você aprenderá a usar a Análise de Vídeo ao vivo no IoT Edge para realizar CVR [gravação de vídeo contínua](continuous-video-recording-concept.md) para a nuvem e transmitir qualquer parte desse vídeo usando os Serviços de Mídia. Isso é útil para cenários como segurança, conformidade e outros em que há a necessidade de manter um arquivo da sequência de imagens de uma câmera por vários dias (ou semanas).

> [!div class="checklist"]
> * Configurar os recursos relevantes
> * Examinar o código que executa a CVR
> * Execute o código de exemplo
> * Examinar os resultados e exibir o vídeo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

É recomendável que você leia as páginas da documentação a seguir

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md) 
* [Cenários de gravação de vídeo contínua](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são os mostrados a seguir

* [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com a extensão do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e a extensão do [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Pode ser solicitado que você instale o Docker. Você pode ignorar esse aviso.
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) instalado no computador de desenvolvimento.
* Conclua o [script de instalação de recursos de Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

Ao final das etapas acima, você terá determinados recursos do Azure implantados na assinatura do Azure, incluindo:

* Hub IoT
* Conta de armazenamento
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) instalado

## <a name="concepts"></a>Conceitos

Conforme explicado [aqui](media-graph-concept.md), um grafo de mídia permite definir de onde a mídia deve ser capturada, como ela deve ser processada e onde os resultados devem ser entregues. Para realizar a CVR, você precisa capturar o vídeo de uma câmera compatível com RTSP e gravá-lo continuamente em um [ativo dos Serviços de Mídia do Azure](terminology.md#asset). O diagrama abaixo mostra uma representação gráfica desse grafo de mídia.

![Grafo de mídia](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

Neste tutorial, você usará um módulo do Edge criado usando o [Servidor de Mídia Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmera RTSP. Dentro do grafo de mídia, você usará um nó de [origem RTSP](media-graph-concept.md#rtsp-source) para obter o feed ao vivo e enviar esse vídeo para o [nó do coletor de ativos](media-graph-concept.md#asset-sink), que gravará o vídeo em um ativo.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Antes de começar, verifique se você concluiu o 3º marcador em [Pré-requisitos](#prerequisites). Quando o script de instalação do recurso for concluído, clique nas chaves para expor a estrutura de pastas. Você verá alguns arquivos criados no diretório ~/clouddrive/lva-sample.

![Configurações do aplicativo](./media/quickstarts/clouddrive.png)

Os arquivos de interesse neste tutorial são:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

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

## <a name="examine-the-sample-files"></a>Examinar os arquivos de exemplo

No Visual Studio Code, abra "src/edge/deployment.template.json". Este modelo define quais módulos de borda serão implantados no dispositivo de borda (a VM Linux do Azure). Observe que há duas entradas na seção "módulos", com os seguintes nomes:

* lvaEdge – essa é a Análise de Vídeo ao vivo no módulo o IoT Edge
* rtspsim – esse é o simulador RTSP

Em seguida, navegue até a pasta "src/cloud-to-device-console-app". Aqui você verá o arquivo appsettings.json criado junto com alguns outros arquivos:

* c2d-console-app.csproj – o arquivo de projeto para o Visual Studio Code.
* operations.json – esse arquivo listará as diferentes operações que você executaria
* Program.cs – o código de exemplo do programa, que faz o seguinte:
    * Carrega as configurações do aplicativo
    * Invoca métodos diretos expostos pela Análise de Vídeo ao vivo no módulo do IoT Edge. Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando [métodos diretos](direct-methods.md)
    * Pausa para você examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela SAÍDA
    * Invoca métodos diretos para limpar recursos

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge 

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto desse tipo no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.

1. Iniciar o Visual Studio Code
1. Defina a cadeia de conexão IoTHub clicando no ícone "mais ações" ao lado do painel HUB IOT DO AZURE no canto inferior esquerdo. Você pode copiar a cadeia de caracteres do arquivo src/cloud-to-device-console-app/appsettings.json. 

    ![Definir a cadeia de conexão de IoT](./media/quickstarts/set-iotconnection-string.png)
1. Clique com o botão direito do mouse no arquivo "src/edge/deployment.template.json" e clique em "Gerar o Manifesto de Implantação do IoT Edge". O Visual Studio Code usa os valores do arquivo .env para substituir as variáveis encontradas no arquivo do modelo de implantação. Isso deve criar um arquivo de manifesto na pasta src/edge/config chamada "deployment.amd64.json".

   ![Gerar um manifesto de implantação do IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Clique com o botão direito do mouse em "src/edge/config/deployment.amd64.json" e clique em "Criar Implantação para Dispositivo Único".

   ![Criar implantação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, você será solicitado a "Selecionar um dispositivo de Hub IoT". Selecione lva-sample-device na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure na seção inferior esquerda e você deverá ver o dispositivo de borda com os seguintes módulos implantados:
    * Análise de Vídeo ao vivo no IoT Edge (nome do módulo "lvaEdge")
    * Simulador RTSP (nome do módulo "rtspsim")
 
    ![Hub IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparar para monitorar os módulos 

Quando você usar o módulo de Análise de Vídeo ao vivo no IoT Edge para registrar o fluxo de vídeo ao vivo, ele enviará eventos para o Hub IoT. Para ver esses eventos, siga estas etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure por Hub IoT do Azure, no canto inferior esquerdo.
1. Expanda o nó Dispositivos.
1. Clique com o botão direito do mouse em lva-sample-device e escolha a opção "Iniciar Monitoramento de Eventos Interno".

    ![Iniciar monitoramento de ponto de extremidade de evento interno](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Execute o programa 

1. Visual Studio Code, navegue até "src/cloud-to-device-console-app/operations.json"
1. Sob o nó GraphTopologySet, edite o seguinte:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Em seguida, sob os nós GraphInstanceSet e GraphTopologyDelete, verifique se o valor de topologyName corresponde ao valor da propriedade "name" na topologia do grafo acima:

    `"topologyName" : "CVRToAMSAsset"`  
1. Abra a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) em um navegador e examine assetNamePattern. Para verificar se você tem um ativo com um nome exclusivo, convém alterar o nome da instância do grafo no arquivo operations.json (do valor padrão de "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
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
1. Quando você pressiona a tecla "Enter" na janela TERMINAL, o próximo conjunto de chamadas de método direto é feito
     * Uma chamada para GraphTopologySet usando a topologyUrl acima.
     * Uma chamada para GraphInstanceSet usando o corpo a seguir.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
1. A instância do grafo continuará sendo executada e gravando o vídeo. O simulador RTSP, por sua vez, continuará fazendo loop do vídeo de origem. Para interromper a gravação, volte para a janela TERMINAL e pressione "Enter". A próxima série de chamadas é feita para limpar os recursos:

     * Uma chamada para GraphInstanceDeactivate para desativar a instância de grafo
     * Uma chamada para GraphInstanceDelete para excluir a instância
     * Uma chamada para GraphTopologyDelete para excluir a topologia
     * Uma chamada final para GraphTopologyList para mostrar que a lista está vazia agora

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando você executa o grafo de mídia, o módulo de Análise de Vídeo ao vivo no IoT Edge envia determinados eventos de diagnóstico e operacionais para o Hub do IoT Edge. Esses eventos são as mensagens que você vê na janela SAÍDA do Visual Studio Code, as quais contêm uma seção "body" e uma seção "applicationProperties". Para entender o que essas seções representam, leia [este](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artigo.

Nas mensagens abaixo, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo Análise de Vídeo ao vivo.

## <a name="diagnostic-events"></a>Eventos de diagnóstico 

### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando a instância do grafo é ativada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no módulo rtspsim. Se for bem-sucedido, ele imprimirá este evento:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* A mensagem é um Evento de diagnóstico, MediaSessionEstablished, que indica que o nó de origem RTSP (o assunto) foi capaz de estabelecer uma conexão com o simulador RTSP e começar a receber um feed ao vivo (simulado).
* O "assunto" em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* "eventType" em applicationProperties indica que se trata de um evento de diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Eventos operacionais 

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando o nó do coletor de ativos começa a gravar vídeo, ele emite esse evento do tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

O "assunto" em applicationProperties faz referência ao nó do coletor de ativos no grafo, que gerou essa mensagem.

O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado. Anote esse valor.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Como o nome sugere, o evento RecordingStarted é enviado quando a gravação é iniciada, mas os dados de vídeo podem ainda não ter sido carregados para o ativo. Quando o nó do coletor de ativos carregou os dados de vídeo no ativo, ele emite este evento do tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

O evento indica que dados suficientes foram escritos no Ativo para que os players/clientes iniciassem a reprodução do vídeo.

O "subject" em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem.

O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Quando você desativa a instância do grafo, o nó do coletor de ativos para de gravar vídeo no ativo e emite esse evento do tipo Microsoft.Media.Graph.Operational.RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Esse evento indica que a gravação foi interrompida.

O "subject" em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem.

O corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo do Serviço de Mídia do Azure no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo dos Serviços de Mídia  

Você pode examinar o ativo dos Serviços de Mídia criado pelo grafo de mídia fazendo logon no portal do Azure e exibindo o vídeo.

1. Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.
1. Localize sua conta dos Serviços de Mídia entre os recursos que você tem em sua assinatura e abra a folha da conta
1. Clique nos Ativos na listagem de Serviços de Mídia

    ![Ativos](./media/continuous-video-recording-tutorial/assets.png)
1. Você encontrará um ativo listado com o nome sampleAsset-CVRToAMSAsset-Sample-Graph-1 – esse é o padrão de nomenclatura escolhido no arquivo de topologia do grafo.
1. Clique no ativo.
1. Na página detalhes do ativo, clique em **Criar** abaixo da caixa de texto da URL de streaming.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que é aberto, aceite as opções padrão e clique em "Adicionar". Para obter mais informações, confira [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Verifique se o [ponto de extremidade de streaming está em execução](../latest/streaming-endpoint-concept.md).
1. O player deve carregar o vídeo e você deverá conseguir clicar em **Reproduzir**>** para exibi-lo.

> [!NOTE]
> Como a origem do vídeo era um contêiner simulando um feed de câmera, os carimbos de data/hora do vídeo estão relacionados aos momentos em que você ativou a instância do grafo e quando a desativou. Você pode ver [este](playback-multi-day-recordings-tutorial.md) tutorial para ver como procurar uma gravação de vários dias e exibir partes desse arquivo. Nesse tutorial, você também poderá ver os carimbos de data/hora no vídeo exibido na tela.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros tutoriais, deve se ater aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://www.onvif.org/conformant-products/) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Você pode seguir as instruções em [Instalar o runtime do Azure IoT Edge no Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e seguir as instruções no guia de início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo Linux virtual](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) para registrar o dispositivo no Hub IoT do Azure.
