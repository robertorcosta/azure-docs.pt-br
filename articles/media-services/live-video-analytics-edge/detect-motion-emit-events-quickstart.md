---
title: Detectar movimento e emitir eventos – Azure
description: Este guia de início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para detectar eventos de movimento e de emissão, chamando programaticamente métodos diretos.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261582"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Início Rápido: Detectar movimento e emitir eventos

Este guia de início rápido orienta você pelas etapas para começar a usar a Análise de Vídeo ao vivo no IoT Edge. Ele usa uma VM do Azure como dispositivo IoT Edge e uma transmissão de vídeo ao vivo simulada. Depois de concluir as etapas de instalação, você poderá executar um fluxo de vídeo ao vivo simulado por meio de um grafo de mídia que detecta e relata qualquer movimento nesse fluxo. O diagrama abaixo mostra uma representação gráfica desse grafo de mídia.

![Análise de Vídeo ao Vivo com base na detecção de movimento](./media/analyze-live-video/motion-detection.png) 

Este artigo é baseado no [código de exemplo](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) escrito em C#.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) em seu computador com as seguintes extensões:
    1. [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) instalado em seu sistema

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

    Se o script for concluído com êxito, você verá todos os recursos mencionados acima em sua assinatura.

1. Quando o script for concluído, clique nas chaves para expor a estrutura de pastas. Você verá alguns arquivos criados no diretório ~/clouddrive/lva-sample. Os itens de interesse neste guia de início rápido são:

     * ~/clouddrive/lva-sample/edge-deployment/.env – contém propriedades que Visual Studio Code usa para implantar módulos em um dispositivo de borda
     * ~/clouddrive/lva-sample/appsetting.json – usado pelo Visual Studio Code para executar o código de exemplo
     
Você precisará deles para atualizar os arquivos no Visual Studio Code mais tarde no guia de início rápido. Você talvez queira copiá-los para um arquivo local por enquanto.


 ![Configurações do aplicativo](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

1. Clone o repositório aqui: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Inicie o Visual Studio Code e abra a pasta para a qual o repositório foi baixado.
1. No Visual Studio Code, navegue até a pasta "src/cloud-to-device-console-app" e crie um arquivo chamado "appsettings.json". Esse arquivo vai conter as configurações necessárias para executar o programa.
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/appsettings.json gerado na seção anterior (confira a etapa 5)

    O texto deve ter esta aparência:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Em seguida, navegue até a pasta "src/edge" e crie um arquivo chamado ".env".
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/edge-deployment/.env. O texto deve ter esta aparência:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Examinar os arquivos de exemplo

1. No Visual Studio Code, navegue até "src/edge". Você verá o arquivo .env criado junto com alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação para o dispositivo de borda com alguns valores de espaço reservado. O arquivo .env tem os valores para essas variáveis.
1. Em seguida, navegue até a pasta "src/cloud-to-device-console-app". Aqui você verá o arquivo appsettings.json criado junto com alguns outros arquivos:

    * c2d-console-app.csproj – o arquivo de projeto para o Visual Studio Code.
    * operations.json – esse arquivo lista as diferentes operações que você gostaria que o programa executasse.
    * Program.cs – o código de exemplo do programa, que faz o seguinte:
    
        * Carrega as configurações do aplicativo
        * Invoca métodos diretos expostos pela Análise de Vídeo ao vivo no módulo do IoT Edge. Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando [métodos diretos](direct-methods.md) 
        * Pausa para você examinar a saída do programa na janela TERMINAL e os eventos gerados pelo módulo na janela SAÍDA
        * Invoca métodos diretos para limpar recursos   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto desse tipo no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.

1. Abra o Visual Studio Code
1. Defina a cadeia de conexão IoTHub clicando no ícone "mais ações" ao lado do painel HUB IOT DO AZURE no canto inferior esquerdo. Você pode copiar a cadeia de caracteres do arquivo src/cloud-to-device-console-app/appsettings.json. 

    ![Definir a cadeia de conexão de IoT](./media/quickstarts/set-iotconnection-string.png)
1. Clique com o botão direito do mouse no arquivo "src/edge/deployment.template.json" e clique em "Gerar o Manifesto de Implantação do IoT Edge".
    ![Gerar um manifesto de implantação do IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Isso deve criar um arquivo de manifesto na pasta src/edge/config chamada "deployment.amd64.json".
1. Clique com o botão direito do mouse em "src/edge/config/deployment.amd64.json" e clique em "Criar Implantação para Dispositivo Único" e selecione o nome do dispositivo de borda.

    ![Criar implantação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, você será solicitado a "Selecionar um dispositivo de Hub IoT". Selecione lva-sample-device na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure na seção inferior esquerda e você deverá ver o dispositivo de borda com os seguintes módulos implantados:

    * Análise de Vídeo ao vivo no IoT Edge (nome do módulo "lvaEdge")
    * Simulador RTSP (nome do módulo "rtspsim")

O módulo do simulador RTSP simula um fluxo de vídeo ao vivo usando um arquivo de vídeo armazenado que foi copiado para o dispositivo de borda quando você executou o [script de instalação dos recursos de Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Neste estágio, os módulos estão implantados, mas nenhum gráfico de mídia está ativo.

## <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Você usará o módulo de Análise de Vídeo ao vivo no IoT Edge para detectar movimento no fluxo de vídeo ao vivo de entrada e enviar eventos para o Hub IoT. Para ver esses eventos, siga estas etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure por Hub IoT do Azure, no canto inferior esquerdo.
1. Expanda o nó Dispositivos.
1. Clique com o botão direito do mouse em lva-sample-device e escolha a opção “Iniciar Monitoramento de Eventos Interno”.

    ![Iniciar monitoramento de ponto de extremidade de evento interno](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

Siga as etapas abaixo para executar o código de exemplo.
1. No Visual Studio Code, navegue até "src/cloud-to-device-console-app/operations.json".
1. Sob o nó GraphTopologySet, assegure o seguinte:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Em seguida, sob os nós GraphInstanceSet e GraphTopologyDelete, verifique se o valor de topologyName corresponde ao valor da propriedade "name" na topologia do grafo acima:

    `"topologyName" : "MotionDetection"`
    
1. Inicie uma sessão de depuração (pressione F5). Você começará a ver algumas mensagens impressas na janela TERMINAL.
1. O operations.json começa com chamadas a GraphTopologyList e GraphInstanceList. Se você tiver limpado os recursos após os guias de início rápido anteriores, isso retornará listas vazias e, em seguida, pausará para que você pressione Enter.

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
         "topologyName": "MotionDetection",
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
     * Uma segunda chamada para GraphInstanceList para mostrar que a instância de grafo está realmente no estado de execução.
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
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

* A mensagem é um Evento de diagnóstico, MediaSessionEstablished, que indica que o nó de origem RTSP (o assunto) foi capaz de estabelecer uma conexão com o simulador RTSP e começar a receber um feed ao vivo (simulado).
* O "assunto" em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* "eventType" em applicationProperties indica que se trata de um evento de diagnóstico.
* "eventTime" indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="motion-detection-event"></a>Evento de detecção de movimento

Quando o movimento é detectado, o módulo de Análise de Vídeo ao vivo no Edge envia um evento de inferência. O tipo é definido como “movimento” para indicar que é resultado do Processador de Detecção de Movimento, e o eventTime informa você em que horário (UTC) o movimento ocorreu. Veja um exemplo:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* "subject" em applicationProperties faz referência ao nó no grafo de mídia do qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó processador de detecção de movimento.
* "eventType" em applicationProperties indica que se trata de um evento de análise.
* "eventTime" indica a hora em que o evento ocorreu.
"body" contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém os dados "timestamp" e "inferences".
* Os dados em "inferences" indicam que o "type" é "motion" e tem dados adicionais sobre o evento do tipo "motion".
* a seção "box" contém as coordenadas de uma caixa delimitadora em volta do objeto móvel. Os valores são normalizados pela largura e altura do vídeo em pixels (por exemplo, largura de 1920 e altura de 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Recursos de limpeza

Se você pretende experimentar outros guias de início rápido, deve ater-se aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este guia de início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Execute os outros guias de início rápido, como a detecção de um objeto em um feed de vídeo ao vivo.        
