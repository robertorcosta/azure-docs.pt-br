---
title: CLI do Azure Service Fabric - repositório sfctl | Microsoft Docs
description: Descreve os comandos do repositório sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: e8a085c4aa6df34441f22da5542231999930d89f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900952"
---
# <a name="sfctl-store"></a>repositório sfctl
Execute operações de arquivo de nível básico no repositório de imagens do cluster.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
| excluir | Exclui o conteúdo do repositório de imagens existente. |
| root-info | Obtém as informações de conteúdo na raiz do repositório de imagens. |
| stat | Obtém as informações de conteúdo do repositório de imagens. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Exclui o conteúdo do repositório de imagens existente.

Exclui o conteúdo do repositório de imagens existente encontrado em determinado caminho relativo do repositório de imagens. Este comando pode ser usado para excluir pacotes de aplicativos carregados quando eles são provisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-store-root-info"></a>informações sobre o sfctl repositório raiz
Obtém as informações de conteúdo na raiz do repositório de imagens.

Retorna as informações sobre o repositório de imagens conteúdo na raiz do repositório de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Obtém as informações de conteúdo do repositório de imagens.

Retorna as informações sobre o conteúdo do repositório de imagem no contentPath especificado. O contentPath é relativo à raiz do repositório de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

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