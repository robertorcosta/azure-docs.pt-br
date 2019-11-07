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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679986"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade Anexar Variável no Azure Data Factory

Use a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
Nome | Nome da atividade no pipeline | Sim
Descrição | Texto descrevendo o que a atividade realiza | não
Tipo | O tipo de atividade é AppendVariable | Sim
valor | Literal de cadeia de caracteres ou valor do objeto de expressão usado para acrescentar à variável especificada | Sim
variableName | Nome da variável que será modificada por atividade, a variável deve ser do tipo "Matriz" | Sim

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir Atividade Variável](control-flow-set-variable-activity.md)
