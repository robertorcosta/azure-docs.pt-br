---
title: Transformação do fluxo de dados de mapeamento de Azure Data Factory existe
description: Como verificar as linhas existentes usando o mapeamento de data factory fluxos de dados com a transformação Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387847"
---
# <a name="mapping-data-flow-exists-transformation"></a>Transformação de fluxo de dados de mapeamento existe



A transformação de ocorrências é uma transformação de filtragem de linhas que impede ou permite que as linhas de dados façam a passagem. A Transformação de Ocorrências é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Após a transformação Exists, as linhas resultantes do fluxo de dados incluirão todas as linhas em que os valores de coluna da origem 1 existem na origem 2 ou não existem na origem 2.

![Configurações existentes](media/data-flow/exists.png "existe 1")

Escolha a segunda fonte para suas ocorrências de modo que o fluxo de dados possa comparar os valores do fluxo 1 com os do fluxo 2.

Selecione as colunas da fonte 1 e da fonte 2 cujos valores quiser verificar se existem ou não.

## <a name="multiple-exists-conditions"></a>Várias condições existentes

Ao lado de cada linha em suas condições de coluna para existir, você encontrará um sinal de + disponível quando passar o mouse sobre a linha de alcance. Isso permitirá que você adicione várias linhas para as condições existentes. Cada condição adicional é um "e".

## <a name="custom-expression"></a>Expressão personalizada

![Existe configurações personalizadas](media/data-flow/exists1.png "existe personalizado")

Você pode clicar em "expressão personalizada" para, em vez disso, criar uma expressão de forma livre como sua condição EXISTS ou NOT-EXISTS. Marcar essa caixa permitirá que você digite sua própria expressão como uma condição.

## <a name="next-steps"></a>Próximos passos

As transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
