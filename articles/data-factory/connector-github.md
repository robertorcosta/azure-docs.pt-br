---
title: Conectar ao GitHub
description: Use o GitHub para especificar suas referências de entidade de modelo de dados comuns
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771029"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Usar o GitHub para ler referências de entidade de modelo de dados comuns

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O conector do GitHub no Azure Data Factory é usado somente para receber o esquema de referência de entidade para o formato de [modelo de dados comum](format-common-data-model.md) no fluxo de dados de mapeamento.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do GitHub.

| Property | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **GitHub**. | sim
| userName | Nome de usuário do GitHub | sim |
| password | Senha do GitHub | sim |

## <a name="next-steps"></a>Próximas etapas

Crie um [conjunto](data-flow-source.md) de dados de origem em mapear o fluxo.