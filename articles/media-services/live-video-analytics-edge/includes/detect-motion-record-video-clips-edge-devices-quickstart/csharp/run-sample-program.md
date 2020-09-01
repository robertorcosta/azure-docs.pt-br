---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682067"
---
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
    
  * Uma chamada a `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo.
  * Uma segunda chamada a `GraphInstanceList` que mostra que a instância do grafo está no estado de execução.
1. A saída na janela **TERMINAL** pausa em `Press Enter to continue`. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá as mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:

    * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
    * Uma chamada para `GraphInstanceDelete` exclui a instância.
    * Uma chamada para `GraphTopologyDelete` exclui a topologia.
    * Uma chamada final para `GraphTopologyList` mostra que agora a lista está vazia.
