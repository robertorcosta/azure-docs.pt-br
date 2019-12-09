---
title: Mapeando nova transformação de Branch de fluxo de dados
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930308"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch

![Opções de ramificação](media/data-flow/menu.png "menu")

A ramificação pegará o fluxo de dados atual no fluxo de dados e irá replicá-lo em outro fluxo. Use a Nova Ramificação para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: seu fluxo de dados tem uma transformação de origem com um conjunto selecionado de colunas e conversões de tipo de dados. Em seguida, coloque uma Coluna Derivada imediatamente após essa Fonte. Na Coluna Derivada, crie um novo campo que combine o nome e sobrenome para formar um novo campo “nome completo”.

Você pode tratar esse novo fluxo com um conjunto de transformações e um coletor em uma linha e usar a Nova Ramificação para criar uma cópia desse fluxo no qual você pode transformar esses mesmos dados com um conjunto diferente de transformações. Ao transformar esses dados copiados em uma ramificação separada, você pode posteriormente coletar esses dados para um local separado.

> [!NOTE]
> “Nova Ramificação” será exibida como uma ação no menu + Transformação quando há uma transformação subsequente no local atual no qual você está tentando ramificar a seguir. ou seja, você não verá uma opção de “Nova Ramificação” no final até que você adicione outra transformação depois de Selecionar

![Branch](media/data-flow/branch2.png "Ramificação 2")
