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
ms.openlocfilehash: af356641b4588529aea25826ff180707ff1ef4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413612"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criando um novo ramo no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando você deseja usar a mesma fonte para vários sinks ou para dados de auto-junção.

Um novo ramo pode ser adicionado da lista de transformação semelhante a outras transformações. **O New Branch** só estará disponível como uma ação quando houver uma transformação existente após a transformação que você está tentando ramificar.

![Adicionando uma nova filial](media/data-flow/new-branch2.png "Adicionando uma nova filial")

No exemplo abaixo, o fluxo de dados está lendo dados de viagem de táxi. A saída agregada por dia e fornecedor é necessária. Em vez de criar dois fluxos de dados separados que lêem da mesma fonte, um novo ramo pode ser adicionado. Dessa forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionando uma nova filial](media/data-flow/new-branch.png "Adicionando uma nova filial")
