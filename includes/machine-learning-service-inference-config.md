---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845156"
---
As entradas no documento `inferenceconfig.json` são mapeadas para os parâmetros da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . A tabela a seguir descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um arquivo local que contém o código a ser executado para a imagem. |
| `runtime` | `runtime` | Opcional. Qual tempo de execução usar para a imagem. Os tempos de execução com suporte são `spark-py` e `python`. Se `environment` for definido, essa entrada será ignorada. |
| `condaFile` | `conda_file` | Opcional. Caminho para um arquivo local que contém uma definição de ambiente Conda a ser usada para a imagem.  Se `environment` for definido, essa entrada será ignorada. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcional. Caminho para um arquivo local que contém etapas adicionais do Docker para executar ao configurar a imagem.  Se `environment` for definido, essa entrada será ignorada.|
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contêm todos os arquivos para criar a imagem, o que facilita o acesso a todos os arquivos nessa pasta ou subpasta. Você pode carregar uma pasta inteira do seu computador local como dependências para o WebService. Observação: os caminhos entry_script, conda_file e extra_docker_file_steps são caminhos relativos para o caminho source_directory. |
| `enableGpu` | `enable_gpu` | Opcional. Se o suporte à GPU deve ser habilitado na imagem. A imagem GPU deve ser usada em um serviço do Azure, como instâncias de contêiner do Azure. Por exemplo, Azure Machine Learning computação, máquinas virtuais do Azure e serviço kubernetes do Azure. O padrão é False. Se `environment` for definido, essa entrada será ignorada.|
| `baseImage` | `base_image` | Opcional. Imagem personalizada a ser usada como uma imagem de base. Se nenhuma imagem base for fornecida, a imagem será baseada no parâmetro de tempo de execução fornecido. Se `environment` for definido, essa entrada será ignorada. |
| `baseImageRegistry` | `base_image_registry` | Opcional. Registro de imagem que contém a imagem base. Se `environment` for definido, essa entrada será ignorada.|
| `cudaVersion` | `cuda_version` | Opcional. Versão do CUDA a ser instalada para imagens que precisam de suporte à GPU. A imagem de GPU deve ser usada em um serviço do Azure. Por exemplo, instâncias de contêiner do Azure, computação Azure Machine Learning, máquinas virtuais do Azure e serviço kubernetes do Azure. As versões com suporte são 9,0, 9,1 e 10,0. Se `enable_gpu` for definido, o padrão será 9,1. Se `environment` for definido, essa entrada será ignorada. |
| `description` | `description` | Uma descrição para a imagem. Se `environment` for definido, essa entrada será ignorada.  |
| `environment` | `environment` | Opcional.  Azure Machine Learning [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

O JSON a seguir é uma configuração de inferência de exemplo para uso com a CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Você pode incluir especificações completas de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning no arquivo de configuração de inferência. Se esse ambiente não existir no seu espaço de trabalho, Azure Machine Learning o criará. Caso contrário, Azure Machine Learning atualizará o ambiente, se necessário. O JSON a seguir é um exemplo:

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

Você também pode usar um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning existente em parâmetros separados da CLI e remover a chave "ambiente" do arquivo de configuração de inferência. Use-e para o nome do ambiente e--EV para a versão do ambiente. Se você não especificar--EV, a versão mais recente será usada. Aqui está um exemplo de um arquivo de configuração de inferência:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

O comando a seguir demonstra como implantar um modelo usando o arquivo de configuração de inferência anterior (chamado myInferenceConfig. JSON). 

Ele também usa a versão mais recente de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning existente (chamado AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
