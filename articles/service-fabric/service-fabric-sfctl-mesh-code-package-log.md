---
title: CLI do Azure Service Fabric – sfctl mesh code-package-log | Microsoft Docs
description: Descreve os comandos do sfctl mesh code-package-log da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901278"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obter os logs para o contêiner do pacote de código especificado para a réplica de um determinado serviço.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
| get | Obtém os logs do contêiner. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtém os logs do contêiner.

Obtém os logs para o contêiner do pacote de código especificado para a réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --code-package-name           [Obrigatório] | O nome do pacote de código do serviço. |
| --replica-name                [Obrigatório] | Nome da réplica do Service Fabric. |
| --service-name                [Obrigatório] | O nome do serviço. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).