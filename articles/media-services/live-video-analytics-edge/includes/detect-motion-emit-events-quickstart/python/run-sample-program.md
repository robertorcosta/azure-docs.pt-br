---
ms.openlocfilehash: 2349939d4997ddc57d0c0c56a21eeec0357bf0ec
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828992"
---
Siga estas etapas para executar o código de exemplo:

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Configurações da Extensão"
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
