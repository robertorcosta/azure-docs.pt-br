---
title: Azure Service Fabric CLI-sfctl rpm
description: Saiba mais sobre a sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para o serviço de gerenciador de reparos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904948"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço de gerenciador de reparo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Aprovar-força | Força a aprovação de determinada tarefa de reparo. |
| excluir | Exclui uma tarefa de reparo concluída. |
| list | Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar força
Força a aprovação de determinada tarefa de reparo.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
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

|Argumento|Descrição|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo concluída a ser excluída. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
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

|Argumento|Descrição|
| --- | --- |
| --filtro do executor | O nome do executor de reparos cujas tarefas declaradas devem ser incluídas na lista. |
| -filtro de estado | Um bitwise OR dos valores a seguir, especificando qual estado de tarefa deve ser incluído na lista de resultados. <ul><li>1 - criada</li><li>2 - Reivindicado</li><li>4 - Preparação</li><li>8 - Aprovado</li><li>16 - Execução</li><li>32 - Restauração</li><li>64 - concluída</li></ul>
| -filtro de id de tarefa | O prefixo de ID da tarefa de reparo a ser correspondido. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
