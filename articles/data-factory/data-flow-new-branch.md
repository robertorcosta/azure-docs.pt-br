---
title: Múltiplas ramificações no mapeamento do fluxo de dados
description: Replicando fluxos de dados no mapeamento do fluxo de dados com várias filiais
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834481"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criando um novo ramo no mapeamento do fluxo de dados

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando você deseja usar a mesma fonte para vários sinks ou para dados de auto-junção.

Um novo ramo pode ser adicionado da lista de transformação semelhante a outras transformações. **O New Branch** só estará disponível como uma ação quando houver uma transformação existente após a transformação que você está tentando ramificar.

![Adicionando uma nova filial](media/data-flow/new-branch2.png "Adicionando uma nova filial")

No exemplo abaixo, o fluxo de dados está lendo dados de viagem de táxi. A saída agregada por dia e fornecedor é necessária. Em vez de criar dois fluxos de dados separados que lêem da mesma fonte, um novo ramo pode ser adicionado. Dessa forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionando uma nova filial](media/data-flow/new-branch.png "Adicionando uma nova filial")
