---
title: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387211"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch



![Opções de ramificação](media/data-flow/menu.png "AdicionarMenu")

A ramificação pegará o fluxo de dados atual no fluxo de dados e irá replicá-lo em outro fluxo. Use a Nova Ramificação para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: seu fluxo de dados tem uma transformação de origem com um conjunto selecionado de colunas e conversões de tipo de dados. Em seguida, coloque uma Coluna Derivada imediatamente após essa Fonte. Na Coluna Derivada, crie um novo campo que combine o nome e sobrenome para formar um novo campo “nome completo”.

Você pode tratar esse novo fluxo com um conjunto de transformações e um coletor em uma linha e usar a Nova Ramificação para criar uma cópia desse fluxo no qual você pode transformar esses mesmos dados com um conjunto diferente de transformações. Ao transformar esses dados copiados em uma ramificação separada, você pode posteriormente coletar esses dados para um local separado.

> [!NOTE]
> “Nova Ramificação” será exibida como uma ação no menu + Transformação quando há uma transformação subsequente no local atual no qual você está tentando ramificar a seguir. ou seja, você não verá uma opção de “Nova Ramificação” no final até que você adicione outra transformação depois de Selecionar

![Ramo](media/data-flow/branch2.png "Ramificação 2")
