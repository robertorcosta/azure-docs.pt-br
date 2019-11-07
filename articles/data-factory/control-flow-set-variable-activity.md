---
title: Definir Atividade Variável no Azure Data Factory
description: Saiba como usar a atividade Definir Variável para definir o valor de uma variável existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679294"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir Atividade Variável no Azure Data Factory

Use a atividade Definir Variável para definir o valor de uma variável existente do tipo Cadeia de caracteres, Booliano ou Matriz definido em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
Nome | Nome da atividade no pipeline | Sim
Descrição | Texto descrevendo o que a atividade realiza | não
Tipo | Tipo de atividade é SetVariable | Sim
valor | Literal de cadeia de caracteres ou valor do objeto de expressão usado para definir a variável especificada | Sim
variableName | Nome da variável que será definida por essa atividade | Sim


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Acrescentar Atividade Variável](control-flow-append-variable-activity.md)
