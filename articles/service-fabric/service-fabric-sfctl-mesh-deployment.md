---
title: CLI do Azure Service Fabric-implantação de malha sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para criar Service Fabric recursos de malha.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645354"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Criar recursos da Malha do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Description|
| --- | --- |
| create | Cria uma implantação de Recursos da Malha do Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Cria uma implantação de Recursos da Malha do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --input-yaml-files [Obrigatório] | Caminhos de arquivo relativos/absolutos separados por vírgula de todos os arquivos YAML ou caminho relativo/absoluto do diretório (recursivo) que contêm arquivos YAML. |
| --parameters | Um caminho relativo/absoluto para o arquivo YAML ou um objeto JSON que contém os parâmetros que precisam ser substituídos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Description|
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

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros que são passados diretamente como objeto JSON
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).