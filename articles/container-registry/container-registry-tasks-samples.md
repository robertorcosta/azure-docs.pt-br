---
title: Amostras de tarefas ACR
description: Amostrar tarefas de registro de contêiner do Azure (ACR Tasks) para construir, executar e patch de imagens de contêiner
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456091"
---
# <a name="acr-tasks-samples"></a>Amostras de Tarefas ACR

Este artigo vincula-se a arquivos de exemplo `task.yaml` e arquivos Docker associados para vários cenários de Tarefas de Registro de Contêiner do [Azure](container-registry-tasks-overview.md) (ACR Tasks). 

Para exemplos adicionais, consulte o repo [amostras do Azure.][task-examples]

## <a name="scenarios"></a>Cenários

* **Construir imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Executar recipiente** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Construir e empurrar imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e executar imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e empurrar várias imagens** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e testar imagens em** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)paralelo , [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e empurrar imagens para vários registros** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Arquivo Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre tarefas acr:

* [Tarefas em várias etapas](container-registry-tasks-multi-step.md) - Fluxos de trabalho baseados em tarefas ACR para construir, testar e remendar imagens de contêineres na nuvem.
* [Referência de tarefa](container-registry-tasks-reference-yaml.md) – tipos de etapa de tarefa, suas propriedades e uso.
* [Repo CMD](https://github.com/AzureCR/cmd) - Uma coleção de contêineres como comandos para tarefas ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
