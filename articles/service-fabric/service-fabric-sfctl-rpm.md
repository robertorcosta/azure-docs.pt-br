---
title: CLI do Azure Service Fabric-sfctl rpm
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para o serviço do Gerenciador de reparo.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639081"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço de gerenciador de reparo.

## <a name="commands"></a>Comandos

|Comando|Description|
| --- | --- |
| Aprovar-força | Força a aprovação de determinada tarefa de reparo. |
| excluir | Exclui uma tarefa de reparo concluída. |
| list | Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar força
Força a aprovação de determinada tarefa de reparo.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-rpm-delete"></a>excluir sfctl rpm
Exclui uma tarefa de reparo concluída.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo concluída a ser excluída. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-rpm-list"></a>lista sfctl rpm
Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --filtro do executor | O nome do executor de reparos cujas tarefas declaradas devem ser incluídas na lista. |
| -filtro de estado | Um bitwise OR dos valores a seguir, especificando qual estado de tarefa deve ser incluído na lista de resultados. <br> 1 - criada <br>2 - declarado  <br>4 - preparação  <br>8 - aprovado  <br>16 - em execução  <br>32 - restaurar  <br>64 - concluída |
| -filtro de id de tarefa | O prefixo de ID da tarefa de reparo a ser correspondido. |

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