---
title: CLI do Azure Service Fabric – gateway de malha sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter e excluir Service Fabric recursos do gateway de malha.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906007"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Obter e excluir recursos de gateway da malha.

## <a name="commands"></a>Comandos

|Comando|Description|
| --- | --- |
| excluir | Exclui o recurso de gateway. |
| list | Lista todos os recursos de gateway. |
| mostrar | Obtém o recurso de gateway com o nome fornecido. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
Exclui o recurso de gateway.

Exclui o recurso de gateway identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de gateway. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
Lista todos os recursos de gateway.

Obtém as informações sobre todos os recursos de gateway em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do gateway.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
Obtém o recurso de gateway com o nome fornecido.

Obtém as informações sobre o recurso de gateway com o nome fornecido. As informações incluem a descrição e outras propriedades do gateway.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de gateway. |

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