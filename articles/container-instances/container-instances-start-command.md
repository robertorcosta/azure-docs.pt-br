---
title: Substituir ponto de entrada na instância do contêiner
description: Defina uma linha de comando para substituir o ponto de entrada em uma imagem de contêiner quando você implantar uma instância de contêiner Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247118"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Defina a linha de comando em uma instância de contêiner para substituir a operação de linha de comando padrão

Quando você criar uma instância de contêiner, especificar opcionalmente um comando para substituir a instrução padrão da linha de comando assada na imagem do contêiner. Este comportamento é `--entrypoint` semelhante ao argumento `docker run`da linha de comando para .

Como definir variáveis de ambiente para [instâncias](container-instances-environment-variables.md) de contêiner, especificar uma linha de comando inicial é útil para trabalhos em lote onde você precisa preparar cada contêiner dinamicamente com configuração específica da tarefa.

## <a name="command-line-guidelines"></a>Diretrizes da linha de comando

* Por padrão, a linha de comando especifica um *único processo que começa sem um shell* no recipiente. Por exemplo, a linha de comando pode executar um script Python ou um arquivo executável. O processo pode especificar parâmetros ou argumentos adicionais.

* Para executar vários comandos, inicie sua linha de comando definindo um ambiente shell que é suportado no sistema operacional do contêiner. Exemplos:

  |Sistema operacional  |Shell padrão  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Siga as convenções da shell para combinar vários comandos para executar em seqüência.

* Dependendo da configuração do contêiner, talvez seja necessário definir um caminho completo para a linha de comando executável ou argumentos.

* Defina uma diretiva de [reinicialização](container-instances-restart-policy.md) apropriada para a instância do contêiner, dependendo se a linha de comando especifica uma tarefa de longa duração ou uma tarefa em execução. Por exemplo, uma `Never` política `OnFailure` de reinicialização ou é recomendada para uma tarefa em execução. 

* Se você precisar de informações sobre o ponto de entrada padrão definido em uma imagem de contêiner, use o comando [Docker Image Inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Sintaxe da linha de comando

A sintaxe da linha de comando varia dependendo da API do Azure ou da ferramenta usada para criar as instâncias. Se você especificar um ambiente de shell, observe também as convenções de sintaxe de comando da shell.

* [az container create][az-container-create] command: Passe `--command-line` uma string com o parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Cmdlet Azure PowerShell: Passe `-Command` uma corda com o parâmetro. Exemplo: `-Command "echo hello"`.

* Portal Azure: Na propriedade **Command override** da configuração do contêiner, forneça uma lista de strings separadas por comuma, sem aspas. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo do Gerenciador de recursos ou arquivo YAML, ou um dos SDKs do Azure: Especifique a propriedade da linha de comando como uma matriz de strings. Exemplo: o array `["python", "myscript.py", "arg1", "arg2"]` JSON em um modelo de Gerenciador de recursos. 

  Se você estiver familiarizado com a sintaxe [do Arquivo Docker,](https://docs.docker.com/engine/reference/builder/) este formato é semelhante à forma *executiva* da instrução cmd.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Comando único | `--command-line "python myscript.py arg1 arg2"` | **Substituição de comando:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Vários comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Substituição de comando:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplos de CLI do Azure

Como exemplo, modifique o comportamento da imagem do contêiner [microsoft/aci-wordcount,][aci-wordcount] que analisa o texto em *Hamlet* de Shakespeare para encontrar as palavras mais freqüentes. Em vez de analisar *Hamlet,* você pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do contêiner [microsoft/aci-wordcount][aci-wordcount] quando analisar o texto padrão, execute-o com o seguinte comando [de criação de contêiner az.][az-container-create] Nenhuma linha de comando start é especificada, então o comando de contêiner padrão é executado. Para fins de ilustração, este exemplo define [variáveis de ambiente](container-instances-environment-variables.md) para encontrar as 3 melhores palavras com pelo menos cinco letras de comprimento:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Uma vez que o estado do contêiner se mostre como *Terminado* (use [az container show][az-container-show] para verificar o estado), exiba o registro com [logs de contêiner az][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora configure um segundo exemplo de contêiner para analisar texto diferente especificando uma linha de comando diferente. O script Python executado pelo contêiner, *wordcount.py,* aceita uma URL como argumento e processa o conteúdo dessa página em vez do padrão.

Por exemplo, para determinar as 3 principais palavras que têm pelo menos cinco letras em *Romeu e Julieta:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois que o contêiner estiver *Encerrado*, exiba a saída, mostrando os logs do contêiner:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Próximas etapas

Cenários baseados em tarefas, como o processamento em lote de um grande conjunto de dados com vários contêineres, podem se beneficiar de linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre a execução de contêineres baseados em tarefas, consulte [Executar tarefas em contêiner com políticas de reinicialização](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
