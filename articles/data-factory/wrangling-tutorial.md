---
title: Introdução ao fluxo de dados do Wrangling no Azure Data Factory
description: Um tutorial sobre como preparar dados no Azure Data Factory usando o fluxo de dados do Wrangling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f32739b06920f6b20dc87b8e1fbd2884c323a859
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633756"
---
# <a name="prepare-data-with-data-wrangling"></a>Preparar dados com o Wrangling de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os Wrangling de dados no data factory permitem que você crie Power Query de mashups interativos nativamente no ADF e, em seguida, execute-os em escala dentro de um pipeline do ADF.

> [!NOTE]
> Power Query atividade no ADF está disponíveis no momento em visualização pública

## <a name="create-a-power-query-activity"></a>Criar uma atividade de Power Query

Há duas maneiras de criar uma Power Query no Azure Data Factory. Uma delas é clicar no ícone de adição e selecionar o **fluxo de dados** no painel recursos de fábrica.

> [!NOTE]
> Anteriormente, o recurso Data Wrangling estava localizado no fluxo de trabalho de fluxo de dados. Agora, você criará seu Wrangling de dados para combinar com ```New > Power query```

![Captura de tela que mostra Power Query no painel recursos de fábrica.](media/data-flow/power-query-wrangling.png)

O outro método está no painel atividades da tela do pipeline. Abra o **Power Query** acordeão e arraste a atividade de **Power Query** para a tela.

![Captura de tela que realça a opção de fluxo de dados Wrangling.](media/data-flow/power-query-activity.png)

## <a name="author-a-wrangling-data-flow"></a>Criar um fluxo de dados do Wrangling

Adicione um **conjunto de fontes de origem** para seu Power Query combinar. Você pode escolher um conjunto de um existente ou criar um novo. Você também pode selecionar um conjunto de um banco de uma coletor. Você pode escolher um ou mais conjuntos de data de origem, mas apenas um coletor é permitido neste momento. A escolha de um conjunto de coletas é opcional, mas pelo menos um conjunto de fonte de origem é necessário.

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Clique em **criar** para abrir o Power Query editor de mashup online.

![Captura de tela que mostra o botão criar que abre o Power Query editor de mashup online.](media/wrangling-data-flow/tutorial5.png)

Crie seu fluxo de dados do Wrangling usando a preparação de dados sem código. Para obter a lista de funções disponíveis, consulte [funções de transformação](wrangling-functions.md). O ADF converte o ```M``` script em um script de fluxo de dados para que você possa executar o Power Query em escala usando o ambiente Spark de fluxo de dados do ADF.

![Captura de tela que mostra o processo de criação do fluxo de dados do Wrangling.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Executando e monitorando um fluxo de dados do Wrangling

Para executar uma depuração de pipeline de execução de um fluxo de dados Wrangling, clique em **depurar** na tela do pipeline. Depois de publicar o fluxo de dados, o **gatilho agora** executa uma execução sob demanda do último pipeline publicado. Os fluxos de dados do Wrangling podem ser agendados com todos os gatilhos de Azure Data Factory existentes.

![Captura de tela que mostra como adicionar um fluxo de dados Wrangling.](media/wrangling-data-flow/tutorial3.png)

Vá para a guia **Monitor** para visualizar a saída de uma execução de atividade de fluxo de dados Wrangling disparada.

![Captura de tela que mostra a saída de uma execução de atividade de fluxo de dados Wrangling disparada.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um fluxo de dados de mapeamento](tutorial-data-flow.md).
