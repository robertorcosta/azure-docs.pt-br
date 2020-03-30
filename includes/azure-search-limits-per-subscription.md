---
title: incluir arquivo
description: incluir arquivo
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272675"
---
Você pode criar vários serviços dentro de uma assinatura. Cada um pode ser provisionado em um nível específico. Você está limitado apenas pelo número de serviços permitidos em cada nível. Por exemplo, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre níveis, consulte [Escolha um SKU ou um nível para pesquisa cognitiva do Azure](../articles/search/search-sku-tier.md).

Os limites de serviço máximos podem ser aumentados mediante solicitação. Se você precisar de mais serviços dentro da mesma assinatura, entre em contato com o Azure Support.

| Recurso            | Gratuito<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Quantidade máxima de serviços    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima nas unidades de busca (SU)<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Gratuito é baseado em recursos compartilhados, não dedicados. Não há suporte para escalar verticalmente nos recursos compartilhados.

<sup>2</sup> As unidades de pesquisa são unidades de cobrança, alocadas como uma *réplica* ou uma *partição*. Você precisa de ambos de recursos para as operações de armazenamento, indexação e consulta. Para saber mais sobre computação de SU, consulte [Escalar os níveis de recursos para cargas de trabalho de índice e consulta](../articles/search/search-capacity-planning.md). 