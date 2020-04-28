---
title: Criação Visual
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418483"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A experiência (UX) da interface do usuário do Azure Data Factory permite criar e implantar visualmente recursos para seu data factory sem ter que gravar nenhum código. Você pode arrastar atividades para uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar as execuções do pipeline.

Atualmente, o Azure Data Factory UX só tem suporte no Microsoft Edge e no Google Chrome.

## <a name="authoring-canvas"></a>Tela de criação

Para abrir a **tela de criação**, clique no ícone de lápis. 

![Tela de criação](media/author-visually/authoring-canvas.png)

Aqui, você criará os pipelines, as atividades, os conjuntos de dados, os serviços vinculados, os fluxos, os gatilhos e os tempos de execução de integração que compõem sua fábrica. Para começar a criar um pipeline usando a tela de criação, consulte [copiar dados usando a atividade de cópia](tutorial-copy-data-portal.md). 

A experiência de criação visual padrão está trabalhando diretamente com o serviço de Data Factory. Azure Repos a integração do git ou do GitHub também tem suporte para permitir o controle do código-fonte e a colaboração para o trabalho em seus pipelines de data factory. Para saber mais sobre as diferenças entre essas experiências de criação, consulte [controle do código-fonte em Azure data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Expressões e funções

As expressões e funções podem ser usadas em vez de valores estáticos para especificar muitas propriedades em Azure Data Factory.

Para especificar uma expressão para um valor de propriedade, selecione **adicionar conteúdo dinâmico** ou clique em **ALT + P** enquanto estiver focalizando o campo.

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-1.png)

Isso abre o **Data Factory Construtor de expressões** , em que é possível criar expressões de variáveis de sistema com suporte, saída de atividade, funções e variáveis ou parâmetros especificados pelo usuário. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem de expressão, consulte [expressões e funções no Azure data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Envie comentários

Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
