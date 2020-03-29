---
title: Implantação de malha CLI-sfctl de malha do Azure Service
description: Saiba mais sobre a sfctl, a interface de linha de comando azure Service Fabric. Inclui uma lista de comandos para criar recursos de malha de malha de malha de malha de malha de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906030"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Criar recursos da Malha do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| create | Cria uma implantação de Recursos da Malha do Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Cria uma implantação de Recursos da Malha do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --input-yaml-files [Obrigatório] | Caminhos de arquivos relativos ou absolutos separados por comma de todos os arquivos de inhame ou caminho relativo ou absoluto do diretório (recursivo) que contenham arquivos de yaml. |
| --parameters | Um caminho relativo ou absoluto para um arquivo de inhame ou um objeto json que contém os parâmetros que precisam ser substituídos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Consolida e implanta todos os recursos no cluster substituindo os parâmetros mencionados no arquivo YAML
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros mencionados no arquivo YAML

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implanta todos os recursos em um diretório para agrupar, substituindo os parâmetros que são passados diretamente como objeto json
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) o CLI do tecido de serviço.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
