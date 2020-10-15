---
title: Analisar vídeos ao vivo com Análise Dinâmica de Vídeo no IoT Edge e na Visão Personalizada do Azure
description: Saiba como usar a Visão Personalizada para criar um modelo em contêineres que possa detectar um caminhão de brinquedos e usar a funcionalidade de extensibilidade de IA de LVA (Análise Dinâmica de Vídeo) no IoT Edge para implantar o modelo na borda para detectar os caminhões de brinquedos de um fluxo de vídeo ao vivo.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: e77521765156a13f0675602ffd0b39f78d8957bb
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016783"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analisar vídeos ao vivo com Análise Dinâmica de Vídeo no IoT Edge e na Visão Personalizada do Azure

Neste tutorial, você vai aprender a usar a [Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) para criar um modelo em contêineres que possa detectar um caminhão de brinquedos e usar a [funcionalidade de extensibilidade de IA](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) de Análise Dinâmica de Vídeo no IoT Edge para implantar o modelo na borda para detectar os caminhões de brinquedos de um fluxo de vídeo ao vivo.

Mostraremos como reunir o poder da Visão Personalizada – que permite que qualquer pessoa crie e treine um modelo de pesquisa visual computacional simplesmente carregando e rotulando algumas imagens sem nenhum conhecimento de ciência de dados, ML ou IA, juntamente com recursos de Análise Dinâmica de Vídeo para implantar facilmente um modelo personalizado como um contêiner na borda e analisar um feed de vídeo ao vivo simulado. Este tutorial usa uma VM do Azure como um dispositivo IoT Edge, é baseado em código de exemplo escrito em C# e se baseia no início rápido [Detectar eventos de movimento e emissão](detect-motion-emit-events-quickstart.md).

Este tutorial mostra como:

> [!div class="checklist"]
> * Configurar os recursos relevantes.
> * Criar um modelo de Visão Personalizada na nuvem para detectar um caminhão de brinquedos e implantá-lo na borda
> * Criar e implantar um grafo de mídia com a extensão http para o modelo de visão personalizada
> * Executar o código de exemplo.
> * Examinar e interpretar os resultados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

É recomendável que você leia os seguintes artigos antes de começar: 

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Visão geral da Visão Personalizada do Azure](../../cognitive-services/custom-vision-service/overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md)
* [Análise de Vídeo ao vivo sem gravação de vídeo](analyze-live-video-concept.md)
* [Como executar a Análise Dinâmica de Vídeo com seu próprio modelo](use-your-model-quickstart.md)
* [Tutorial: Desenvolver um módulo do IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Como editar deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos deste tutorial são:

* [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com as extensões do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Pode ser solicitado que você instale o Docker. Ignore esse aviso.
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) instalado no computador de desenvolvimento.
* Verifique se você tem:
    
    * [Configurar recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Configurar seu ambiente de desenvolvimento](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

Este tutorial usa um arquivo de [vídeo de inferência de carros de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para simular uma transmissão ao vivo. Você pode examinar o vídeo por meio de um aplicativo, como [player de mídia do VLC](https://www.videolan.org/vlc/). Selecione Ctrl+N e cole um link para o [vídeo de inferência de carros de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para iniciar a reprodução. Ao assistir ao vídeo, observe que, no marcador de 36 segundos, um caminhão de brinquedo aparece no vídeo. O modelo personalizado foi treinado para detectar esse caminhão de brinquedo específico. Neste tutorial, você usará a Análise Dinâmica de Vídeo no IoT Edge para detectar o caminhões de brinquedos e publicar eventos de inferência associados no Hub do IoT Edge.

## <a name="overview"></a>Visão geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Visão geral da Visão Personalizada":::

O diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de filtro de taxa de quadros](media-graph-concept.md#frame-rate-filter-processor). Esse processador limita a taxa de quadros do fluxo de vídeo que alcança o nó do [processador de extensão HTTP](media-graph-concept.md#http-extension-processor).
O nó de extensão HTTP desempenha a função de um proxy. Ele converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem por REST para outro módulo de borda que executa um modelo de IA por trás de um ponto de extremidade HTTP. Neste exemplo, esse módulo de borda é o modelo de detector de caminhão de brinquedos criado usando a Visão Personalizada. O nó de processador de extensão HTTP reúne os resultados da detecção e publica eventos no nó do [coletor do Hub IoT](media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [Hub do IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Criar e implantar um modelo de detecção de brinquedo de Visão Personalizada 

Como o nome Visão Personalizada sugere, você pode usá-lo para criar seu próprio detector de objeto personalizado ou classificador na nuvem. Ele fornece uma interface simples, fácil de usar e intuitiva para criar modelos de visão personalizada que podem ser implantados na nuvem ou na borda por meio de contêineres. 

Para criar um detector de caminhão de brinquedo, recomendamos que você siga o [artigo de início rápido](../../cognitive-services/custom-vision-service/get-started-build-detector.md) Criar um detector de objeto via portal da Web da Visão personalizada.

Observações adicionais:
 
* Para este tutorial, não use as imagens de exemplo fornecidas na [seção de Pré-requisito](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) do artigo de início rápido. Em vez disso, usamos um determinado conjunto de imagens para criar um modelo de visão personalizada do detector de brinquedos. Sugerimos que você use [estas imagens](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) quando for solicitado a [escolher suas imagens de treinamento](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) no guia de início rápido.
* Na seção de imagem de marcação do início rápido, verifique se você está marcando o caminhão de brinquedo visto na imagem com a marca – "caminhão de entrega".

Ao terminar, se o modelo estiver pronto de modo satisfatório, você poderá exportá-lo para um contêiner do Docker usando o botão Exportar na guia Desempenho. Escolha Linux como o tipo de plataforma de contêiner. Esta é a plataforma na qual o contêiner será executado. O computador no qual você baixa o contêiner pode ser Windows ou Linux. As instruções a seguir foram baseadas no arquivo de contêiner baixado para um computador Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Visão geral da Visão Personalizada"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Esse comando testa o contêiner no computador local e, se a imagem tiver o mesmo caminhão de entrega em que treinamos o modelo, a saída deverá ser algo semelhante ao seguinte, sugerindo que o caminhão de entrega foi detectado com 90,12% de probabilidade.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Examinar os arquivos de exemplo

1. No VSCode, navegue até "src/edge". Você verá o arquivo .env criado junto com alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação para o dispositivo de borda com alguns valores de espaço reservado. O arquivo .env tem os valores para essas variáveis.
1. Em seguida, navegue até a pasta "src/cloud-to-device-console-app". Aqui você verá o arquivo appsettings.json criado junto com alguns outros arquivos:

    * c2d-console-app.csproj – esse é o arquivo de projeto para o VSCode.
    * operations.json – esse arquivo listará as diferentes operações que você gostaria que o programa executasse.
    * Program.cs – este é o código de exemplo do programa, que faz o seguinte:

        * Carrega as configurações do aplicativo.
        * Invoque a Análise Dinâmica de Vídeo nos métodos diretos do módulo do IoT Edge para criar topologia, instanciar o grafo e ativar o grafo.
        * Pausa para examinar a saída do grafo na janela TERMINAL e os eventos enviados ao Hub IoT na janela OUTPUT.
        * Desative a instância do grafo, exclua a instância do grafo e exclua a topologia do grafo.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implantar o manifesto de implantação

1. No VSCode, navegue até "src/cloud-to-device-console-app/operations.json"

1. Em GraphTopologySet, verifique se o seguinte é verdadeiro:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Em GraphInstanceSet, verifique o seguinte: 
    1. "topologyName": "InferencingWithHttpExtension"
    1. Adicione o seguinte à parte superior da matriz de parâmetros – `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Altere o valor do parâmetro rtspUrl para – "rtsp://rtspsim:554/media/t2.mkv"    
1. Em GraphTopologyDelete, verifique "name": "InferencingWithHttpExtension"
1. Clique com o botão direito do mouse no arquivo "src/edge/deployment.customvision.template.json" e clique em **Gerar o Manifesto de Implantação do IoT Edge**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Visão geral da Visão Personalizada" ao lado do painel HUB IOT DO AZURE no canto inferior esquerdo. Você pode copiar a cadeia de caracteres do arquivo appsettings.json. (Aqui está outra abordagem recomendada para garantir que você tenha o Hub IoT adequado configurado no VSCode por meio do [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Visão geral da Visão Personalizada" e clique em **Criar implantação para dispositivo único**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Visão geral da Visão Personalizada":::
1. Em seguida, você será solicitado a selecionar um dispositivo de Hub IoT. Selecione lva-sample-device na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure na seção inferior esquerda e você deverá ter o dispositivo de borda com os seguintes módulos implantados:

    * A Análise Dinâmica de Vídeo no módulo do IoT Edge, chamada de "lvaEdge".
    * Um módulo chamado `rtspsim` que simula um servidor RTSP, agindo como a origem de um feed de vídeo ao vivo.
    * Um módulo chamado `cv`, que, como o nome sugere, é o modelo de detecção de caminhão de brinquedo da Visão Personalizada que aplica a visão personalizada às imagens e retorna vários tipos de marca. (Nosso modelo foi treinado em apenas uma marca – "caminhão de entrega").

## <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Clique com o botão direito do mouse no dispositivo de Análise Dinâmica de Vídeo e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela SAÍDA do Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Visão geral da Visão Personalizada":::

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

Se você abrir a topologia de grafo para este tutorial em um navegador, verá que o valor de inferencingUrl foi definido como http://cv:80/image, o que significa que o servidor de inferência retornará resultados após a detecção de caminhões de brinquedo, se houver, no vídeo ao vivo.

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Visão geral da Visão Personalizada":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visão geral da Visão Personalizada"
              }
            ]
          }
        }
   ```
    
   * Uma chamada para GraphInstanceActivate que inicia a instância do grafo e o fluxo de vídeo.
   * Uma segunda chamada para GraphInstanceList que mostra que a instância do grafo está em estado de execução.
    
1. A saída na janela TERMINAL é pausada no aviso Clique em ENTER para continuar. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela SAÍDA no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste tutorial aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela TERMINAL e selecione Enter.
A próxima série de chamadas limpa os recursos:
    
   * Uma chamada para GraphInstanceDeactivate desativa a instância do grafo.
   * Uma chamada para GraphInstanceDelete exclui a instância.
   * Uma chamada para GraphTopologyDelete exclui a topologia.
   * Uma chamada final para GraphTopologyList mostra que a lista está vazia.
    
## <a name="interpret-the-results"></a>Interpretar os resultados

Quando você executa o grafo de mídia, os resultados do nó do processador de extensão HTTP passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela SAÍDA contêm uma seção body e uma seção applicationProperties. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso. O tipo de evento é Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Nesta mensagem, observe estes detalhes:

* A mensagem é um evento de diagnóstico. MediaSessionEstablished indica que o nó de origem RTSP (o assunto) se conectou com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em applicationProperties, subject indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em applicationProperties, eventType indica que esse evento é de diagnóstico.
* O eventTime indica a hora em que o evento ocorreu.
* O corpo contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do contêiner de Visão Personalizada e emite os resultados por meio do nó do coletor do Hub IoT como eventos de inferência.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Observe o seguinte nas mensagens acima:

* O subject em applicationProperties faz referência ao nó no MediaGraph do qual a mensagem foi gerada. Nesse caso, a mensagem é originada do processador de Extensão Http.
* O eventType em applicationProperties indica que se trata de um evento de Inferência de Análise.
* O eventTime indica a hora em que o evento ocorreu.
* "body" contém dados sobre o evento de análise. Nesse caso, o evento é de inferência, portanto, o corpo contém uma matriz de inferências chamada "previsões".
* A seção "previsões" contém a lista de previsões em que o caminhão de entrega de brinquedo (marca = caminhão de entrega) é encontrado no quadro. Como você poderia lembrar, o caminhão de entrega é a marca personalizada que você forneceu ao seu modelo treinado personalizado para o caminhão de brinquedo e o modelo está realizando a inferência e identificando o caminhão de brinquedos no vídeo de entrada com diferentes pontuações de confiança de probabilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros tutoriais ou guias de início rápido, deve ater-se aos recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Pesquise pelas câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 em vez de usar uma VM Linux do Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge-linux.md). 

Depois, registre o dispositivo no Hub IoT do Azure seguindo as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md).