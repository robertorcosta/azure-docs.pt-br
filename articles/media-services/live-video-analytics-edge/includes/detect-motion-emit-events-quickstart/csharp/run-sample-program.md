---
ms.openlocfilehash: ed7be82146f38cc7ae57fd863bb0c1b8e6910fd2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690881"
---
Siga estas etapas para executar o código de exemplo:

1. No Visual Studio Code, navegue até *src/cloud-to-device-console-app/operations.json*.
1. Sob o nó **GraphTopologySet**, verifique se você vê o seguinte valor:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Sob os nós **GraphInstanceSet** e **GraphTopologyDelete**, verifique se o valor de `topologyName` corresponde ao valor da propriedade `name` na topologia do grafo:

    `"topologyName" : "MotionDetection"`
    
1. Inicie uma sessão de depuração pressionando F5. A janela **TERMINAL** exibirá algumas mensagens.
1. O arquivo *operations.json* começa com chamadas para `GraphTopologyList` e `GraphInstanceList`. Se você limpou os recursos após a conclusão dos inícios rápidos anteriores, esse processo retornará listas vazias e, em seguida, pausará. Para continuar, pressione Enter.

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
     
    * Uma chamada a `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo.
    * Uma segunda chamada a `GraphInstanceList` que mostra que a instância do grafo está no estado de execução.
1. A saída na janela **TERMINAL** pausa em `Press Enter to continue`. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:

    * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
    * Uma chamada para `GraphInstanceDelete` exclui a instância.
    * Uma chamada para `GraphTopologyDelete` exclui a topologia.
    * Uma chamada final para `GraphTopologyList` mostra que a lista está vazia.
