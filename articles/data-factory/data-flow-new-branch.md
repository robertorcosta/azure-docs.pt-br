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
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606404"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criando um novo ramo no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando você deseja usar a mesma fonte para vários sinks ou para dados de auto-junção.

Um novo ramo pode ser adicionado da lista de transformação semelhante a outras transformações. **O New Branch** só estará disponível como uma ação quando houver uma transformação existente após a transformação que você está tentando ramificar.

![Adicionando uma nova filial](media/data-flow/new-branch2.png "Adicionando uma nova filial")

No exemplo abaixo, o fluxo de dados está lendo dados de viagem de táxi. A saída agregada por dia e fornecedor é necessária. Em vez de criar dois fluxos de dados separados que lêem da mesma fonte, um novo ramo pode ser adicionado. Dessa forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionando uma nova filial](media/data-flow/new-branch.png "Adicionando uma nova filial")
