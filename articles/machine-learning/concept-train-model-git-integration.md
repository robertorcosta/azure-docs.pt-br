---
title: Integração git para Aprendizado de Máquina do Azure
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se integra a um repositório git local. Ao enviar um treinamento executado a partir de um diretório local que é um repositório git, as informações sobre repo, branch e current commit são rastreadas como parte da execução.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402825"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração git para Aprendizado de Máquina do Azure

[Git](https://git-scm.com/) é um sistema de controle de versão popular que permite compartilhar e colaborar em seus projetos. 

O Azure Machine Learning suporta totalmente os repositórios do Git para o rastreamento do trabalho - você pode clonar repositórios diretamente no seu sistema de arquivos de espaço de trabalho compartilhado, usar o Git em sua estação de trabalho local ou usar o Git a partir de um pipeline de CI/CD.

Ao enviar um trabalho para o Azure Machine Learning, se os arquivos de origem forem armazenados em um repositório git local, as informações sobre o repo serão rastreadas como parte do processo de treinamento.

Como o Azure Machine Learning rastreia informações de um repo git local, ele não está vinculado a nenhum repositório central específico. Seu repositório pode ser clonado do GitHub, GitLab, Bitbucket, Azure DevOps ou qualquer outro serviço compatível com git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clone os repositórios do Git no seu sistema de arquivos de espaço de trabalho
O Azure Machine Learning fornece um sistema de arquivos compartilhados para todos os usuários no espaço de trabalho.
Para clonar um repositório do Git neste compartilhamento de arquivos, recomendamos que você crie uma Instância de Cálculo & abra um terminal.
Uma vez que o terminal é aberto, você tem acesso a um cliente Git completo e pode clonar e trabalhar com o Git através da experiência Git CLI.

Recomendamos que você clone o repositório em seu diretório de usuários para que outros não façam colisões diretamente em seu ramo de trabalho.

Você pode clonar qualquer repositório do Git que você possa autenticar (GitHub, Azure Repos, BitBucket, etc.)

Para um guia sobre como usar o Git CLI, leia [aqui](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Código de trilha que vem dos repositórios do Git

Quando você envia uma corrida de treinamento do Python SDK ou machine learning CLI, os arquivos necessários para treinar o modelo são carregados para o seu espaço de trabalho. Se `git` o comando estiver disponível no seu ambiente de desenvolvimento, o processo de upload o usará para verificar se os arquivos estão armazenados em um repositório git. Se assim for, então as informações do seu repositório git também são carregadas como parte da corrida de treinamento. Essas informações são armazenadas nas seguintes propriedades para a execução do treinamento:

| Propriedade | Comando Git usado para obter o valor | Descrição |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI do que seu repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI do que seu repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a execução foi submetida. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a execução foi submetida. |
| `azureml.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se o ramo/confirmação estiver sujo; caso contrário, `false`. |

Essas informações são enviadas para execução que usam um estimador, um pipeline de aprendizado de máquina ou um script executado.

Se seus arquivos de treinamento não estiverem localizados em um repositório git no seu ambiente de desenvolvimento ou o `git` comando não estiver disponível, então nenhuma informação relacionada ao git será rastreada.

> [!TIP]
> Para verificar se o comando git está disponível no ambiente de desenvolvimento, abra uma sessão de shell, prompt de comando, PowerShell ou outra interface de linha de comando e digite o seguinte comando:
>
> ```
> git --version
> ```
>
> Se instalado e no caminho, você recebe `git version 2.4.1`uma resposta semelhante a . Para obter mais informações sobre a instalação do git no seu ambiente de desenvolvimento, consulte o site do [Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Veja as informações registradas

As informações do git são armazenadas nas propriedades para uma corrida de treinamento. Você pode visualizar essas informações usando o portal Azure, Python SDK e CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. No [portal Azure,](https://portal.azure.com)selecione seu espaço de trabalho.
1. Selecione __Experimentos__e selecione um de seus experimentos.
1. Selecione uma das executões da coluna NÚMERO DE __EXECUÇÃO.__
1. Selecione __Logs__e expanda os logs e as __entradas__ __do azureml.__ Selecione o link que começa com __ ### \_o azure__.

    ![A entrada ###_azure no portal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

As informações registradas contêm texto semelhante ao seguinte JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>SDK do Python

Depois de enviar uma corrida de treinamento, um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) é devolvido. O `properties` atributo deste objeto contém as informações registradas do git. Por exemplo, o código a seguir recupera o hash de confirmação:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O `az ml run` comando CLI pode ser usado para recuperar as propriedades de uma corrida. Por exemplo, o comando a seguir retorna as `train-on-amlcompute`propriedades para a última execução no experimento denominado :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para obter mais informações, consulte a documentação de referência [de execução de az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Próximas etapas

* [Configurar e usar metas de computação para treinamento de modelos](how-to-set-up-training-targets.md)
