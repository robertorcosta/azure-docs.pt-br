---
title: Mapeamento do fluxo de dados Monitoramento visual
description: Como monitorar visualmente os Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 18099e853aa44e4434a14d7ea913f968593021ec
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687909"
---
# <a name="monitor-data-flows"></a>Monitorar Fluxo de Dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Após concluir a criação e depuração do fluxo de dados, é altamente recomendável programar o fluxo de dados para executar em um agendamento dentro do contexto de um pipeline. É possível agendar o pipeline a partir do Azure Data Factory usando Gatilhos. Ou, você pode usar a opção Disparar Agora do Construtor de Pipeline do Azure Data Factory para executar uma execução única e testar o fluxo de dados no contexto de pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades contidas no pipeline, incluindo a atividade de Fluxo de Dados. Clique no ícone de monitoramento no painel esquerdo da interface do usuário do Azure Data Factory. Uma tela semelhante à seguinte será exibida. Os ícones realçados permitirão que você analise detalhadamente as atividades no pipeline, incluindo a atividade de Fluxo de Dados.

![Monitoramento do fluxo de dados](media/data-flow/mon001.png "Monitoramento de fluxo de dados")

Você verá estatísticas neste nível, incluindo os tempos de execução e status. A ID de Execução no nível de atividade é diferente da ID de Execução no nível de pipeline. A ID de Execução no nível anterior é para o pipeline. Ao clicar no ícone de óculos, os detalhes sobre a execução do fluxo de dados serão exibidos detalhadamente.

![Monitoramento do fluxo de dados](media/data-flow/mon002.png "Monitoramento de fluxo de dados")

Quando você estiver na exibição de monitoramento de nós de gráfico, uma versão somente exibição simplificada do grafo do fluxo de dados será exibida.

![Monitoramento do fluxo de dados](media/data-flow/mon003.png "Monitoramento de fluxo de dados")

Aqui está uma visão geral do vídeo do desempenho de monitoramento de seus fluxos de dados a partir da tela de monitoramento do ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Exibir Planos de Execução do Fluxo de Dados

Quando o fluxo de dados é executado no Spark, a Fábrica de Dados do Azure determina os caminhos de código ideais com base na totalidade do fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do fluxo, levando em conta o caminho de execução de suas transformações. Ao clicar em nós individuais, você verá "agrupamentos" que representam o código que foi executado em conjunto no cluster. As contagens e intervalos exibidos representam esses grupos, ao contrário das etapas individuais no design.

![Monitoramento do fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

* Ao clicar no espaço aberto na janela de monitoramento, as estatísticas no painel inferior exibem as contagens de linha e tempo para cada Coletor e as transformações que conduziram aos dados do coletor para a linhagem de transformação.

* Quando você seleciona transformações individuais, você receberá feedback adicional no painel à direita que mostra estatísticas de partição, contagem de colunas, distorção (quão uniformemente os dados são distribuídos entre partições) e kurtose (quão espinhosos são os dados).

* Ao clicar no Coletor na exibição do nó, a linhagem da coluna é exibida. Há três métodos diferentes em que as colunas são acumuladas ao longo do fluxo de dados para chegar ao Coletor. Eles são:

  * Computado: Você usa a coluna para processamento condicional ou dentro de uma expressão em seu fluxo de dados, mas não aterrissa na Pia
  * Derivado: A coluna é uma nova coluna que você gerou em seu fluxo, ou seja, não estava presente na Fonte
  * Mapeado: A coluna originou-se da fonte e você está mapeando-a para um campo de pia
  * Status do fluxo de dados: O status atual da sua execução
  * Tempo de inicialização do cluster: quantidade de tempo para adquirir o ambiente de computação JIT Spark para sua execução de fluxo de dados
  * Número de transformações: Quantas etapas de transformação estão sendo executadas no seu fluxo
  
![Monitoramento do fluxo de dados](media/data-flow/monitornew.png "Monitoramento de fluxo de dados novo")  
  
## <a name="monitor-icons"></a>Ícones de monitoramento

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, o caminho de execução e os intervalos levaram isso em consideração:

![Monitoramento do fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

Você também verá ícones de círculo verde na transformação. Eles representam uma contagem do número de coletores para os quais os dados estão fluindo.
