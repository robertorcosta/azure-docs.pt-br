---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159397"
---
As entradas `inferenceconfig.json` no mapa de documentos para os parâmetros da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um arquivo local que contém o código a ser executado para a imagem. |
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contêm todos os arquivos para criar a imagem, o que facilita o acesso a quaisquer arquivos dentro desta pasta ou subpasta. Você pode carregar uma pasta inteira da sua máquina local como dependências para o Webservice. Nota: seus caminhos entry_script, conda_file e extra_docker_file_steps são caminhos relativos para o caminho source_directory. |
| `environment` | `environment` | Opcional.  Ambiente de aprendizado de máquina [do Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Você pode incluir especificações completas de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Aprendizado de Máquina do Azure no arquivo de configuração de inferência. Se esse ambiente não existir no seu espaço de trabalho, o Azure Machine Learning irá criá-lo. Caso contrário, o Azure Machine Learning atualizará o ambiente se necessário. O seguinte JSON é um exemplo:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Você também pode usar um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de aprendizado de máquina existente em parâmetros CLI separados e remover a chave "ambiente" do arquivo de configuração de inferência. Use -e para o nome do ambiente e --ev para a versão do ambiente. Se você não especificar --ev, a versão mais recente será usada. Aqui está um exemplo de um arquivo de configuração de inferência:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

O comando a seguir demonstra como implantar um modelo usando o arquivo de configuração de inferência anterior (chamado myInferenceConfig.json). 

Ele também usa a versão mais recente de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Aprendizado de Máquina azure existente (chamado AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
