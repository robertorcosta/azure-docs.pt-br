---
title: Purgar tags e manifestos
description: Use um comando de purga para excluir várias tags e manifestos de um registro de contêiner do Azure com base na idade e no filtro de marca, e, opcionalmente, agende as operações de expurgo.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087336"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Expurgar automaticamente imagens de um registro de contêiner do Azure

Quando você usa um registro de contêiner do Azure como parte de um fluxo de trabalho de desenvolvimento, o registro pode rapidamente preencher com imagens ou outros artefatos que não são necessários após um curto período. Você pode querer excluir todas as tags que são mais antigas do que uma determinada duração ou corresponder a um filtro de nome especificado. Para excluir vários artefatos rapidamente, este `acr purge` artigo introduz o comando que você pode executar como uma tarefa ACR demanda ou [programada.](container-registry-tasks-scheduled.md) 

O `acr purge` comando está atualmente distribuído em`mcr.microsoft.com/acr/acr-cli:0.1`uma imagem de contêiner público ( ), construído a partir do código fonte no repo [acr-cli](https://github.com/Azure/acr-cli) no GitHub.

Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI para executar os exemplos de tarefas aCR neste artigo. Se você quiser usá-lo localmente, a versão 2.0.69 ou posterior é necessária. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

> [!WARNING]
> Use `acr purge` o comando com cautela - os dados de imagem excluídos são IRRECUPERÁVEIS. Se você tem sistemas que puxam imagens por digestão manifesto (ao contrário do nome da imagem), você não deve limpar imagens não marcadas. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de receber pelo manifesto, considerar a adoção de uma *marcação exclusiva* esquema, um [melhor prática recomendada](container-registry-image-tag-version.md).

Se você quiser excluir marcas de imagem única ou manifestos usando comandos Azure CLI, consulte [Excluir imagens de contêiner no Registro de Contêineres do Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Use o comando purga

O `acr purge` comando container exclui imagens por tag em um repositório que corresponda a um filtro de nome e que são mais antigos do que uma duração especificada. Por padrão, apenas as referências de tag são excluídas, não os [manifestos subjacentes](container-registry-concepts.md#manifest) e os dados de camada. O comando tem a opção de também excluir manifestos. 

> [!NOTE]
> `acr purge`não exclui uma tag de imagem ou `write-enabled` repositório onde o atributo é definido como `false`. Para obter informações, consulte [Bloquear uma imagem de contêiner em um registro de contêiner Do Zure](container-registry-image-lock.md).

`acr purge`é projetado para ser executado como um comando de contêiner em uma [tarefa ACR,](container-registry-tasks-overview.md)de modo que ele autentica automaticamente com o registro onde a tarefa é executada e executa ações lá. Os exemplos de tarefas `acr purge` neste artigo usam o [alias de](container-registry-tasks-reference-yaml.md#aliases) comando no lugar de um comando de imagem de contêiner totalmente qualificado.

No mínimo, especifique `acr purge`o seguinte ao executar:

* `--filter`- Um repositório e uma *expressão regular* para filtrar tags no repositório. Exemplos: `--filter "hello-world:.*"` corresponde a todas `hello-world` as tags no `--filter "hello-world:^1.*"` repositório e corresponde a tags que começam com `1`. Passe `--filter` vários parâmetros para limpar vários repositórios.
* `--ago`- Uma [seqüência](https://golang.org/pkg/time/) de duração estilo Go para indicar uma duração além da qual as imagens são excluídas. A duração consiste em uma seqüência de um ou mais números decimais, cada um com um sufixo unitário. As unidades de tempo válidas incluem "d" para dias, "h" por horas e "m" por minutos. Por exemplo, `--ago 2d3h6m` seleciona todas as imagens filtradas por último modificadas `--ago 1.5h` há mais de 2 dias, 3 horas e 6 minutos atrás, e seleciona imagens modificadas pela última vez há mais de 1,5 horas.

`acr purge`suporta vários parâmetros opcionais. Os dois a seguir são usados em exemplos neste artigo:

* `--untagged`- Os manifestos que não possuem tags*associadas (manifestos não marcados) são excluídos.*
* `--dry-run`- Especifica que nenhum dado é excluído, mas a saída é a mesma que se o comando for executado sem este sinalizador. Este parâmetro é útil para testar um comando de purga para garantir que ele não exclua inadvertidamente os dados que você pretende preservar.

Para parâmetros `acr purge --help`adicionais, execute . 

`acr purge`suporta outros recursos de comandos ACR Tasks, incluindo [variáveis de execução](container-registry-tasks-reference-yaml.md#run-variables) e [logs de execução de tarefas](container-registry-tasks-logs.md) que são transmitidos e também salvos para recuperação posterior.

### <a name="run-in-an-on-demand-task"></a>Executar em uma tarefa demanda

O exemplo a seguir usa o comando `acr purge` [az acr run][az-acr-run] para executar o comando sob demanda. Este exemplo exclui todas as tags `hello-world` de imagem e manifestos no repositório no *myregistry* que foram modificados há mais de 1 dia. O comando container é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Executar em uma tarefa programada

O exemplo a seguir usa o comando [az acr task create][az-acr-task-create] para criar uma [tarefa ACR agendada](container-registry-tasks-scheduled.md)diária . A tarefa expurga tags modificadas há `hello-world` mais de 7 dias no repositório. O comando container é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Expurgar um grande número de tags e manifestos

A eliminação de um grande número de tags e manifestos pode levar vários minutos ou mais. Para limpar milhares de tags e manifestos, o comando pode precisar ser executado por mais tempo do que o tempo de tempo padrão de 600 segundos para uma tarefa demanda ou 3600 segundos para uma tarefa programada. Se o tempo de tempo é excedido, apenas um subconjunto de tags e manifestos serão excluídos. Para garantir que um expurgo em `--timeout` larga escala esteja completo, passe o parâmetro para aumentar o valor. 

Por exemplo, a tarefa a seguir demanda define um tempo de tempo de 3600 segundos (1 hora):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exemplo: Expurgo programado de vários repositórios em um registro

Este exemplo passa `acr purge` por uso para limpar periodicamente vários repositórios em um registro. Por exemplo, você pode ter um pipeline `samples/devimage1` de `samples/devimage2` desenvolvimento que empurra imagens para os repositórios. Você importa periodicamente imagens de desenvolvimento em um repositório de produção para suas implantações, para que você não precise mais das imagens de desenvolvimento. Semanalmente, você limpa `samples/devimage1` os `samples/devimage2` repositórios, em preparação para o trabalho da próxima semana.

### <a name="preview-the-purge"></a>Visualizar o expurgo

Antes de excluir dados, recomendamos executar uma `--dry-run` tarefa de purga demanda usando o parâmetro. Esta opção permite que você veja as tags e manifesta que o comando será expurgado, sem remover nenhum dado. 

No exemplo a seguir, o filtro em cada repositório seleciona todas as tags. O `--ago 0d` parâmetro corresponde a imagens de todas as idades nos repositórios que correspondem aos filtros. Modifique os critérios de seleção conforme necessário para o seu cenário. O `--untagged` parâmetro indica a exclusão de manifestos, além de tags. O comando container é passado para o comando [az acr run][az-acr-run] usando uma variável de ambiente.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Revise a saída de comando para ver as tags e manifestos que correspondem aos parâmetros de seleção. Como o comando `--dry-run`é executado com , nenhum dado é excluído.

Saída de exemplo:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Agende o expurgo

Depois de verificar o dry run, crie uma tarefa programada para automatizar a purgação. O exemplo a seguir agenda uma tarefa semanal no domingo às 13:00 UTC para executar o comando de expurgo anterior:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

## <a name="next-steps"></a>Próximas etapas

Conheça outras opções para [excluir dados](container-registry-delete.md) de imagem no Registro de Contêineres do Azure.

Para obter mais informações sobre o armazenamento de imagens, consulte [o armazenamento de imagens do contêiner container no Registro de Contêineres do Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

