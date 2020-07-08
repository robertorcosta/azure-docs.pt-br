---
title: CLI do Azure Service Fabric-pacote de código de malha sfctl-log
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter logs para um pacote de códigos especificado.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906055"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obter os logs para o contêiner do pacote de código especificado para a réplica de um determinado serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
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
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).