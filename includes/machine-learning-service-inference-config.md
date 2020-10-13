---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748773"
---
As entradas no documento `inferenceconfig.json` são mapeadas para os parâmetros da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). A seguinte tabela descreve o mapeamento entre as entidades no documento JSON e os parâmetros do método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um arquivo local que contém o código a ser executado para a imagem. |
| `sourceDirectory` | `source_directory` | Opcional. Caminho para as pastas que contêm todos os arquivos para criar a imagem, o que facilita o acesso a arquivos dessa pasta ou subpasta. É possível fazer upload de uma pasta inteira do computador local como dependências do Webservice. Observação: entry_script, conda_file e extra_docker_file_steps são caminhos relativos para o caminho source_directory. |
| `environment` | `environment` | Opcional.  [Ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) do Azure Machine Learning.|

É possível incluir especificações completas de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) do Azure Machine Learning no arquivo de configuração da inferência. Se esse ambiente não existir no seu workspace, o Azure Machine Learning o criará. Caso contrário, se necessário, o Azure Machine Learning atualizará o ambiente. Este JSON é um exemplo:

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
                            "scikit-learn==0.22.1",
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

Também é possível usar um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) existente do Azure Machine Learning em parâmetros separados da CLI e remover a chave do "ambiente" do arquivo de configuração da inferência. Use -e para o nome do ambiente e --ev para a versão do ambiente. Se você não especificar --ev, a versão mais recente será usada. Veja um exemplo de arquivo de configuração de inferência:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

O comando a seguir demonstra como implantar um modelo usando o arquivo de configuração de inferência anterior, chamado myInferenceConfig.json. 

Ele também usa a versão mais recente de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) existente do Azure Machine Learning, chamado AzureML-Minimal.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
