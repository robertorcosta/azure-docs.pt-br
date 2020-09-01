---
ms.openlocfilehash: 4277007938f664592432080ec9799b5b64d98f5c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684134"
---
### <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo

Como parte dos pré-requisitos, você baixou o código de exemplo para uma pasta. Siga estas etapas para examinar e editar os arquivos de exemplo.

1. No Visual Studio Code, vá até *src/edge*. Você verá seu arquivo *.env* e alguns arquivos do modelo de implantação.

    O modelo de implantação se refere ao manifesto de implantação do dispositivo de borda. Ele inclui alguns valores de espaço reservado. O arquivo *.env* inclui os valores dessas variáveis.
1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:

    * ***c2d-console-app.csproj*** – o arquivo de projeto para o Visual Studio Code.
    * ***operations.json*** – uma lista das operações que você deseja que o programa execute.
    * ***Program.cs*** – o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](../../../direct-methods.md).
        * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos que foram gerados pelo módulo na janela **SAÍDA**.
        * Invoca métodos diretos para limpar recursos.
1. Edite o arquivo *operations.json*:
    * Altere o link para a topologia do grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * Em `GraphInstanceSet`, edite o nome da topologia de grafo para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Em `GraphTopologyDelete`, edite o nome:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

1. Clique com o botão direito do mouse no arquivo *src/edge/ deployment.yolov3.template.json* e selecione **Gerar o Manifesto de Implantação do IoT Edge**.

    ![Gerar um Manifesto de Implantação do IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    O arquivo de manifesto *deployment.yolov3.amd64.json* é criado na pasta *src/edge/config*.
1. Se você concluiu o início rápido [Detectar movimento e emitir eventos](../../../detect-motion-emit-events-quickstart.md), ignore esta etapa. 

    Caso contrário, ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **Definir Cadeia de Conexão do Hub IoT**. Você pode copiar a cadeia de caracteres do arquivo *appsettings.json*. Ou, para garantir que configurou o Hub IoT adequado dentro do Visual Studio Code, use o [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do Hub IoT](../../../media/quickstarts/set-iotconnection-string.png)
1. Clique com o botão direito do mouse em *src/edge/config/ deployment.yolov3.amd64.json* e selecione **Criar Implantação para Dispositivo Único**. 

    ![Criar implantação para dispositivo único](../../../media/quickstarts/create-deployment-single-device.png)
1. Quando receber um prompt para selecionar um dispositivo do Hub IoT, selecione **lva-sample-device**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:

    * O módulo da Análise Dinâmica de Vídeo, chamado `lvaEdge`.
    * O módulo `rtspsim`, que simula um servidor RTSP e funciona como a origem de um feed de vídeo ao vivo.

        > [!NOTE]
        > Se você estiver usando o próprio dispositivo de borda em vez de um provisionado pelo nosso script de instalação, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador**, para efetuar pull e armazenar o arquivo de vídeo de exemplo usado neste guia de início rápido:  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
        * O módulo `yolov3`, que é o modelo de detecção de objetos YoloV3 que aplica a Pesquisa Visual Computacional às imagens e retorna várias classes de tipos de objetos
 
      ![Módulos que são implantados no dispositivo de borda](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Preparar-se para monitorar eventos

Clique com o botão direito do mouse no dispositivo de Análise Dinâmica de Vídeo e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela **SAÍDA** do Visual Studio Code. 

![Começar a monitorar](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Executar o programa de exemplo

1. Para iniciar uma sessão de depuração, pressione F5. Você verá mensagens impressas na janela **TERMINAL**.
1. O código *operations.json* começa fazendo chamadas para os métodos diretos `GraphTopologyList` e `GraphInstanceList`. Se você limpou os recursos após a conclusão dos inícios rápidos anteriores, esse processo retornará listas vazias e, em seguida, pausará. Para continuar, pressione Enter.

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

     * Uma chamada para `GraphTopologySet` que usa o `topologyUrl` anterior
     * Uma chamada para `GraphInstanceSet` que usa o seguinte corpo:

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

     * Uma chamada para `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo
     * Uma segunda chamada para `GraphInstanceList` que mostra que a instância do grafo está em estado de execução
1. A saída na janela **TERMINAL** pausa em um prompt `Press Enter to continue`. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:
      * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
      * Uma chamada para `GraphInstanceDelete` exclui a instância.
      * Uma chamada para `GraphTopologyDelete` exclui a topologia.
      * Uma chamada final para `GraphTopologyList` mostra que a lista está vazia.
