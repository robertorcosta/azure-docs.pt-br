---
title: CLI do Azure Service Fabric-pacote de código de malha sfctl-log
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter logs para um pacote de códigos especificado.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646136"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obter os logs para o contêiner do pacote de código especificado para a réplica de um determinado serviço.

## <a name="commands"></a>Comandos

|Comando|Description|
| --- | --- |
| get | Obtém os logs do contêiner. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtém os logs do contêiner.

Obtém os logs para o contêiner do pacote de código especificado para a réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --code-package-name           [Obrigatório] | O nome do pacote de código do serviço. |
| --replica-name                [Obrigatório] | Nome da réplica do Service Fabric. |
| --service-name                [Obrigatório] | O nome do serviço. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).