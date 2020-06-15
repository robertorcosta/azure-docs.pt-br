---
title: Monitoramento visual do fluxo de dados de mapeamento
description: Como monitorar visualmente os Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 9594a2ddfaa0103e171618925ba6974bf9ad7f00
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833958"
---
# <a name="monitor-data-flows"></a>Monitorar Fluxo de Dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Após concluir a criação e a depuração do fluxo de dados, recomendamos programá-lo para executar em um agendamento dentro do contexto de um pipeline. É possível agendar o pipeline a partir do Azure Data Factory usando Gatilhos. Ou, você pode usar a opção Disparar Agora do Construtor de Pipeline do Azure Data Factory para executar uma execução única e testar o fluxo de dados no contexto de pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades que ele contém, incluindo a atividade do Fluxo de Dados. Clique no ícone de monitoramento no painel esquerdo da interface do usuário do Azure Data Factory. Uma tela semelhante à seguinte será exibida. Os ícones realçados permitem que você analise detalhadamente as atividades no pipeline, incluindo a atividade de Fluxo de Dados.

![Monitoramento de fluxo de dados](media/data-flow/mon001.png "Monitoramento de fluxo de dados")

Você verá as estatísticas nesse nível, incluindo os tempos de execução e o status. A ID de execução no nível de atividade é diferente da ID de execução no nível de pipeline. A ID de Execução no nível anterior é para o pipeline. Selecionar o ícone de óculos permite examinar os detalhes da sua execução de fluxo de dados.

![Monitoramento de fluxo de dados](media/data-flow/mon002.png "Monitoramento de fluxo de dados")

Quando estiver na exibição de monitoramento de nós de gráfico, você verá uma versão simplificada do grafo que não pode ser editada do fluxo de dados.

![Monitoramento de fluxo de dados](media/data-flow/mon003.png "Monitoramento de fluxo de dados")

Veja um vídeo de visão geral do desempenho de monitoramento dos seus fluxos de dados na tela de monitoramento do ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Exibir Planos de Execução do Fluxo de Dados

Quando o Fluxo de Dados é executado no Spark, o Azure Data Factory determina os caminhos de código ideais com base na totalidade do fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do fluxo, levando em conta o caminho de execução de suas transformações. Ao selecionar nós individuais, você poderá ver “agrupamentos” que representam o código que foi executado junto no cluster. As contagens e intervalos exibidos representam esses grupos, ao contrário das etapas individuais no design.

![Monitoramento de fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

* Ao selecionar o espaço aberto na janela de monitoramento, as estatísticas no painel inferior exibem as contagens de linha e tempo para cada coletor e as transformações que conduziram aos dados do coletor para a linhagem de transformação.

* Ao selecionar transformações individuais, você receberá comentários adicionais no painel direito que mostrará as estatísticas de partição, contagens de colunas, assimetria (como os dados estão distribuídos uniformemente em partições) e curtose (se os dados têm muitos picos).

* Ao selecionar o coletor na exibição do nó, a linhagem da coluna será exibida. Há três métodos diferentes em que as colunas são acumuladas ao longo do fluxo de dados para chegar ao Coletor. Eles são:

  * Computado: Você usa a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a coloca no coletor
  * Derivado: A coluna é uma nova coluna que você gerou no fluxo, ou seja, ela não estava presente na origem
  * Mapeado: A coluna é originada da origem e você está mapeando-a para um campo do coletor
  * Status do fluxo de dados: O status atual da execução
  * Tempo de inicialização do cluster: Tempo necessário para adquirir o ambiente de computação do Spark JIT para sua execução de fluxo de dados
  * Número de transformações: Quantas etapas de transformação estão sendo executadas no seu fluxo
  
![Monitoramento de fluxo de dados](media/data-flow/monitornew.png "Novo monitoramento do Fluxo de Dados")  
  
## <a name="monitor-icons"></a>Ícones de monitoramento

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, o caminho de execução e os intervalos levaram isso em consideração:

![Monitoramento de fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

Você também verá ícones de círculo verde na transformação. Eles representam uma contagem do número de coletores para os quais os dados estão fluindo.
