---
title: YAML reference - ACR Tasks
description: Referência para definir tarefas na YAML para tarefas do ACR, incluindo propriedades da tarefa, tipos de etapas, propriedades das etapas e variáveis internas.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454641"
---
# <a name="acr-tasks-reference-yaml"></a>Referência das Tarefas do ACR: YAML

A definição de tarefas com várias etapas em Tarefas do ACR fornece um primitivo de computação centrado em contêiner, com foco em compilar, testar e corrigir contêineres. This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

Este artigo contém uma referência para criar arquivos YAML de tarefas com várias etapas para Tarefas do ACR. Se você quiser uma introdução às Tarefas do ACR, confira a [Visão geral das Tarefas de ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Formato de arquivo acr-task.yaml

As Tarefas do ACR dão suporte à declaração de tarefas com várias etapas na sintaxe YAML padrão. You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. Embora este artigo se refira a `acr-task.yaml` como o arquivo que contém as etapas, as Tarefas do ACR dão suporte a qualquer nome de arquivo válido com uma [extensão com suporte](#supported-task-filename-extensions).

Os primitivos `acr-task.yaml` de nível superior são **propriedades das tarefas**, **tipos de etapas** e **propriedades das etapas**:

* As [propriedades das tarefas](#task-properties) se aplicam a todas as etapas em toda a execução da tarefa. There are several global task properties, including:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* Os [tipos de etapas das tarefas](#task-step-types) representam os tipos de ações que podem ser executadas em uma tarefa. Há três tipos de etapas:
  * `build`
  * `push`
  * `cmd`
* As [propriedades das etapas das tarefas](#task-step-properties) são parâmetros que se aplicam a uma etapa individual. Existem várias propriedades das etapas, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muito mais.

O formato base de um arquivo `acr-task.yaml`, incluindo algumas propriedades das etapas comuns, segue. Apesar de não ser uma representação completa de todas as propriedades das etapas disponíveis ou do uso do tipo de etapa, fornece uma rápida visão geral do formato de arquivo básico.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Extensões de nome de arquivo das tarefas com suporte

As Tarefas do ACR reservaram diversas extensões de nome de arquivo, incluindo `.yaml`, que processará como um arquivo de tarefas. Qualquer extensão que *não* constar na lista a seguir será considerada pelas Tarefas do ACR como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML é o único formato de arquivo com suporte, no momento, pelas Tarefas do ACR. As outras extensões de nome de arquivo estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Vários arquivos de tarefas de exemplo são referenciados nas próximas seções deste artigo. The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo pressupõe que você configurou um Registro padrão na CLI do Azure; portanto, o parâmetro `--registry` é omitido. To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

Por exemplo, para configurar a CLI do Azure com um Registro padrão denominado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades das tarefas

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. Algumas dessas propriedades globais podem ser substituídas em uma etapa individual.

| Propriedade | Type | Opcional | Descrição | Substituição com suporte | Valor padrão |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | SIM | A versão do arquivo `acr-task.yaml` conforme analisado pelo serviço de Tarefas do ACR. Enquanto as Tarefas do ACR se esforçam para manter a compatibilidade com versões anteriores, esse valor permite que as Tarefas do ACR mantenham a compatibilidade dentro de uma versão definida. If unspecified, defaults to the latest version. | Não | Nenhum |
| `stepTimeout` | int (segundos) | SIM | O número máximo de segundos em que uma etapa pode ser executada. If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | SIM | 600 (10 minutos) |
| `workingDirectory` | string | SIM | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | SIM | `$HOME` |
| `env` | [string, string, ...] | SIM |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | Nenhum |
| `secrets` | [secret, secret, ...] | SIM | Array of [secret](#secret) objects. | Nenhum |
| `networks` | [network, network, ...] | SIM | Array of [network](#network) objects. | Nenhum |

### <a name="secret"></a>segredo

The secret object has the following properties.

| Propriedade | Type | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | The identifier of the secret. | Nenhum |
| `keyvault` | string | SIM | The Azure Key Vault Secret URL. | Nenhum |
| `clientID` | string | SIM | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | Nenhum |

### <a name="network"></a>rede

The network object has the following properties.

| Propriedade | Type | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | O nome da rede. | Nenhum |
| `driver` | string | SIM | The driver to manage the network. | Nenhum |
| `ipv6` | bool | SIM | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | bool | SIM | Whether to skip network creation. | `false` |
| `isDefault` | bool | SIM | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>Tipos de etapas das tarefas

As Tarefas do ACR dão suporte a três tipos de etapas. Cada tipo de etapa dá suporte a várias propriedades, detalhadas na seção para cada tipo de etapa.

| Tipo de etapa | Descrição |
| --------- | ----------- |
| [`build`](#build) | Compila uma imagem de contêiner usando a sintaxe `docker build` familiar. |
| [`push`](#push) | Executa um `docker push` de imagens recentemente compiladas ou remarcadas em um registro de contêiner. Há suporte para o Registro de Contêiner do Azure, outros Registros privados e o Hub do Docker público. |
| [`cmd`](#cmd) | Executa um contêiner como comando, com os parâmetros passados para o `[ENTRYPOINT]` do contêiner. The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

## <a name="build"></a>compilar

Compilar uma imagem de contêiner. O tipo de etapa `build` representa um meio seguro e com vários locatários de executar `docker build` na nuvem como primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: compilar

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O tipo de etapa `build` suporta os parâmetros na tabela a seguir. O tipo de etapa `build` também suporta todas as opções de construção do comando [docker build](https://docs.docker.com/engine/reference/commandline/build/), como `--build-arg` para definir variáveis de tempo de criação.

| . | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o `image:tag` totalmente qualificado da imagem compilada.<br /><br />Como as imagens podem ser utilizadas para validações de tarefas internas, como testes funcionais, nem todas as imagens exigem `push` para um Registro. No entanto, para criar uma instância de uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para fazer referência.<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. Com as Tarefas do ACR, o cenário padrão pressupõe a capacidade de compilar, validar e efetuar push de uma imagem. Confira [push](#push) para saber como efetuar push de imagens compiladas opcionalmente. | SIM |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, o Dockerfile padrão na raiz do contexto será considerado. To specify a Dockerfile, pass the filename relative to the root of the context. | SIM |
| `context` | O diretório raiz passado para `docker build`. O diretório raiz de cada tarefa é definido como um [workingDirectory](#task-step-properties) compartilhado e inclui a raiz do diretório clonado Git associado. | Não |

### <a name="properties-build"></a>Propriedades: compilar

O tipo de etapa `build` dá suporte às propriedades a seguir. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: compilar

#### <a name="build-image---context-in-root"></a>Compilar imagem – contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilar imagem – contexto no subdiretório

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>efetuar push

Efetuar push de uma ou mais imagens compiladas ou remarcadas em um registro de contêiner. Dá suporte ao push de Registros privados como o Registro de Contêiner do Azure ou o Hub do Docker público.

### <a name="syntax-push"></a>Sintaxe: efetuar push

O tipo de etapa `push` dá suporte a uma coleção de imagens. A sintaxe de coleção YAML dá suporte a formatos embutidos e aninhados. O push de uma única imagem geralmente é representado usando a sintaxe embutida:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Para maior legibilidade, use a sintaxe aninhada ao efetuar push de várias imagens:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: efetuar push

O tipo de etapa `push` dá suporte às propriedades a seguir. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |

### <a name="examples-push"></a>Exemplos: efetuar push

#### <a name="push-multiple-images"></a>Efetuar push de várias imagens

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilar, efetuar push e executar

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O tipo de etapa `cmd` executa um contêiner.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O tipo de etapa `cmd` dá suporte às propriedades a seguir:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

É possível encontrar detalhes sobre essas propriedades na seção [Propriedades das etapas das tarefas](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem olá, mundo

Este comando executa o arquivo da tarefa `hello-world.yaml`, que faz referência à imagem [olá, mundo](https://hub.docker.com/_/hello-world/) no Hub do Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem Bash e ecoar "olá, mundo"

Este comando executa o arquivo da tarefa `bash-echo.yaml`, que faz referência à imagem [bash](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar a tag da imagem Bash específica

Para executar uma versão de imagem específica, especifique a tag no `cmd`.

Este comando executa o arquivo da tarefa `bash-echo-3.yaml`, que faz referência à imagem [bash:3.0](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O tipo de etapa `cmd` faz referência a imagens usando o formato `docker run` padrão. As imagens não precedidas por um registro são consideradas originárias de docker.io. O exemplo anterior também poderia ser representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. Se estiver fazendo referência a imagens no mesmo Registro em que uma Tarefa do ACR estiver em execução, você não precisará especificar credenciais do Registro.

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Propriedades das etapas das tarefas

Cada tipo de etapa dá suporte a várias propriedades apropriadas para seu tipo. A tabela a seguir define todas as propriedades das etapas disponíveis. Nem todos os tipos de etapas dão suporte a todas as propriedades. Para ver quais dessas propriedades estão disponíveis para cada tipo de etapa, confira as seções de referência de tipo de etapa [cmd](#cmd), [compilar](#build) e [efetuar push](#push).

| Propriedade | Type | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | SIM | Se o contêiner deve ser desanexado quando está em execução. | `false` |
| `disableWorkingDirectoryOverride` | bool | SIM | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | string | SIM | Substitui o `[ENTRYPOINT]` do contêiner de uma etapa. | Nenhum |
| `env` | [string, string, ...] | SIM | Matriz de cadeias de caracteres no formato `key=value` que definem as variáveis de ambiente para a etapa. | Nenhum |
| `expose` | [string, string, ...] | SIM | Array of ports that are exposed from the container. |  Nenhum |
| [`id`](#example-id) | string | SIM | Identifica a etapa dentro da tarefa com exclusividade. Outras etapas na tarefa podem fazer referência ao `id` da etapa, como para verificação de dependência com `when`.<br /><br />O `id` também é o nome do contêiner em execução. Processos em execução em outros contêineres na tarefa podem consultar o `id` como seu nome de host DNS ou para acessá-lo com logs de docker [id], por exemplo. | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | bool | SIM | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | string | SIM | The isolation level of the container. | `default` |
| `keep` | bool | SIM | Se o contêiner da etapa deve ser mantido após a execução. | `false` |
| `network` | objeto | SIM | Identifies a network in which the container runs. | Nenhum |
| `ports` | [string, string, ...] | SIM | Array of ports that are published from the container to the host. |  Nenhum |
| `pull` | bool | SIM | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | bool | SIM | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | SIM | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | SIM | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | int (segundos) | SIM | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | objeto | SIM | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | Nenhum |
| `startDelay` | int (segundos) | SIM | Number of seconds to delay a container's execution. | 0 |
| `timeout` | int (segundos) | SIM | Número máximo de segundos em que uma etapa poderá ser executada antes de terminar. | 600 |
| [`when`](#example-when) | [string, string, ...] | SIM | Configura a dependência de uma etapa em relação a uma ou mais etapas diferentes dentro da tarefa. | Nenhum |
| `user` | string | SIM | The user name or UID of a container | Nenhum |
| `workingDirectory` | string | SIM | Define o diretório de trabalho para uma etapa. Por padrão, as Tarefas do ACR criam um diretório raiz como o diretório de trabalho. No entanto, se o build tiver várias etapas, as etapas anteriores poderão compartilhar artefatos com as etapas posteriores se o mesmo diretório de trabalho for especificado. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exemplos: propriedades das etapas das tarefas

#### <a name="example-id"></a>Exemplo: id

Compile duas imagens, criando uma instância de uma imagem de teste funcional. Cada etapa é identificada por um `id` exclusivo, ao qual as outras etapas da tarefa fazem referência na propriedade `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A propriedade `when` especifica a dependência de uma etapa em relação a uma ou mais etapas dentro da tarefa. Ela dá suporte a dois valores de parâmetro:

* `when: ["-"]` – Indica que não há dependência de outras etapas. Uma etapa que especifica `when: ["-"]` iniciará a execução imediatamente e habilita a execução em etapas simultânea.
* `when: ["id1", "id2"]` – Indica que a etapa é dependente das etapas com `id` "id1" e `id` "id2". Essa etapa não será executada até a conclusão das etapas "id1" e "id2".

Se `when` não estiver especificado em uma etapa, essa etapa dependerá da conclusão da etapa anterior no arquivo `acr-task.yaml`.

Execução em etapas sequenciais sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução em etapas sequenciais com `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallel images build:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilação de imagens paralelas e teste dependente:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As Tarefas do ACR incluem um conjunto padrão de variáveis que estão disponíveis para as etapas das tarefas durante a execução. Essas variáveis podem ser acessadas usando o formato `{{.Run.VariableName}}`, em que `VariableName` é um dos seguintes:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. A ID representa a execução que está sendo executada no momento.

Normalmente usado para uma marcação exclusiva de uma imagem:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do Registro. Normalmente usado para fazer uma referência genérica ao registro em que a tarefa está sendo executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

O horário UTC atual em que a execução começou.

### <a name="runcommit"></a>Run.Commit

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>Aliases

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| Alias | Run variable |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Próximos passos

Para uma visão geral das tarefas com várias etapas, confira [Executar tarefas com várias etapas de compilar, testar e corrigir nas Tarefas do ACR](container-registry-tasks-multi-step.md).

Para builds de uma única etapa, confira a [Visão geral das Tarefas do ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
