---
title: Monitoramento Visual de Fluxo de Dados do Mapeamento do Azure Data Factory
description: Como monitorar visualmente os Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 41b4b7cd911bad40055fcf527c186f8de9466cdc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030280"
---
# <a name="monitor-data-flows"></a>Monitorar Fluxo de Dados



Após concluir a criação e depuração do fluxo de dados, é altamente recomendável programar o fluxo de dados para executar em um agendamento dentro do contexto de um pipeline. É possível agendar o pipeline a partir do Azure Data Factory usando Gatilhos. Ou, você pode usar a opção Disparar Agora do Construtor de Pipeline do Azure Data Factory para executar uma execução única e testar o fluxo de dados no contexto de pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades contidas no pipeline, incluindo a atividade de Fluxo de Dados. Clique no ícone de monitoramento no painel esquerdo da interface do usuário do Azure Data Factory. Uma tela semelhante à seguinte será exibida. Os ícones realçados permitirão que você analise detalhadamente as atividades no pipeline, incluindo a atividade de Fluxo de Dados.

(media/data-flow/mon001.png "Monitoramento de fluxo de dados") de monitoramento de fluxo de ![dados]

Você verá estatísticas nesse nível, bem como os tempos de execução e o status. A ID de Execução no nível de atividade é diferente da ID de Execução no nível de pipeline. A ID de Execução no nível anterior é para o pipeline. Ao clicar no ícone de óculos, os detalhes sobre a execução do fluxo de dados serão exibidos detalhadamente.

(media/data-flow/mon002.png "Monitoramento de fluxo de dados") de monitoramento de fluxo de ![dados]

Quando você estiver na exibição de monitoramento de nós de gráfico, uma versão somente exibição simplificada do grafo do fluxo de dados será exibida.

(media/data-flow/mon003.png "Monitoramento de fluxo de dados") de monitoramento de fluxo de ![dados]

## <a name="view-data-flow-execution-plans"></a>Exibir Planos de Execução do Fluxo de Dados

Quando o fluxo de dados é executado no Spark, Azure Data Factory determina caminhos de código ideais com base na totalidade de seu fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do fluxo, levando em conta o caminho de execução de suas transformações. Ao clicar em nós individuais, você verá "agrupamentos" que representam o código que foi executado em conjunto no cluster. As contagens e intervalos exibidos representam esses grupos, ao contrário das etapas individuais no design.

(media/data-flow/mon004.png "Monitoramento de fluxo de dados") de monitoramento de fluxo de ![dados]

* Ao clicar no espaço aberto na janela de monitoramento, as estatísticas no painel inferior exibem as contagens de linha e tempo para cada Coletor e as transformações que conduziram aos dados do coletor para a linhagem de transformação.

* Ao selecionar transformações individuais, você receberá comentários adicionais no painel à direita que mostra estatísticas de partição, contagens de colunas, distorção (como uniformemente os dados são distribuídos entre partições) e curtose (como os com picos são os dados).

* Ao clicar no Coletor na exibição do nó, a linhagem da coluna é exibida. Há três métodos diferentes em que as colunas são acumuladas ao longo do fluxo de dados para chegar ao Coletor. São eles:

  * Computado: Você usa a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a coloca no Coletor
  * Derivado: A coluna é uma nova coluna que você gerou no fluxo, ou seja, ela não estava presente na Origem
  * Mapeado: A coluna é originada da origem e você está mapeando-a para um campo do coletor
  
## <a name="monitor-icons"></a>Ícones de monitoramento

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, o caminho de execução e os intervalos levaram isso em consideração:

(media/data-flow/mon004.png "Monitoramento de fluxo de dados") de monitoramento de fluxo de ![dados]

Você também verá ícones de círculo verde na transformação. Eles representam uma contagem do número de coletores para os quais os dados estão fluindo.
