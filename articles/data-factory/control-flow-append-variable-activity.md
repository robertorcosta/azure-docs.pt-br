---
title: Atividade Anexar Variável no Azure Data Factory
description: Saiba como definir a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679986"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade Anexar Variável no Azure Data Factory

Use a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade no pipeline | Sim
descrição | Texto descrevendo o que a atividade realiza | não
type | O tipo de atividade é AppendVariable | sim
value | Literal de cadeia de caracteres ou valor do objeto de expressão usado para acrescentar à variável especificada | sim
variableName | Nome da variável que será modificada por atividade, a variável deve ser do tipo "Matriz" | sim

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir Atividade Variável](control-flow-set-variable-activity.md)
