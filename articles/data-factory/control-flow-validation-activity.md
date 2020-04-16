---
title: Atividade de validação na fábrica de dados do Azure
description: A atividade validação não continua a execução do pipeline até que valide o conjunto de dados anexado com certos critérios especificados pelo usuário.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417922"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação na fábrica de dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Você pode usar uma Validação em um pipeline para garantir que o pipeline só continue a execução depois de validar a referência do conjunto de dados anexado, que ele atenda aos critérios especificados ou que o tempo foi alcançado.


## <a name="syntax"></a>Sintaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade 'Validação' | String | Sim |
type | Deve ser definido como **Validação**. | String | Sim |
dataset | A atividade bloqueará a execução até que ela tenha validado essa referência de conjunto de dados existe e que ela atenda aos critérios especificados ou o tempo foi alcançado. O conjunto de dados fornecido deve oferecer suporte à propriedade "MinimumSize" ou "ChildItems". | Referência de conjunto de dados | Sim |
tempo limite | Especifica o tempo limite para a atividade ser executada. Se nenhum valor for especificado, o valor padrão será de 7 dias ("7.00:00:00"). O formato é d.hh:mm:ss | String | Não |
sleep | Um atraso em segundos entre as tentativas de validação. Se nenhum valor for especificado, o valor padrão será de 10 segundos. | Integer | Não |
childItems | Verifica se a pasta tem itens infantis. Pode ser definido como verdadeiro : Valide que a pasta existe e que ela tem itens. Blocos até que pelo menos um item esteja presente na pasta ou o valor do tempo é alcançado.-falso: Valide que a pasta existe e que ela está vazia. Bloqueia até que a pasta esteja vazia ou até que o valor do tempo total seja atingido. Se nenhum valor for especificado, a atividade será bloqueada até que a pasta exista ou até que o tempo de intervalo seja atingido. | Boolean | Não |
Minimumsize | Tamanho mínimo de um arquivo em bytes. Se nenhum valor for especificado, o valor padrão será 0 bytes | Integer | Não |


## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
