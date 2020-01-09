---
title: CLI do Azure Service Fabric-serviço de malha sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter detalhes de serviço para um recurso de aplicativo.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 1ae04d054a254746d59b85f4fe366cebf19e3faf
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646085"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obter detalhes do serviço e listar os serviços de um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|Description|
| --- | --- |
| list | Lista todos os recursos de serviço. |
| mostrar | Obtém o recurso de serviço com o nome fornecido. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Lista todos os recursos de serviço.

Obtém as informações sobre todos os serviços de um recurso de aplicativo. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Obtém o recurso de serviço com o nome fornecido.

Obtém as informações sobre o recurso de serviço com o nome fornecido. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --name -n                     [Obrigatório] | O nome do serviço. |

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