---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477248"
---
Para atualizar um serviço `update` web, use o método. Você pode atualizar o serviço web para usar um novo modelo, um novo script de entrada ou novas dependências que podem ser especificadas em uma configuração de inferência. Para obter mais informações, consulte a documentação [para Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Quando você cria uma nova versão de um modelo, você deve atualizar manualmente cada serviço que deseja usá-lo.
>
> Você não pode usar o SDK para atualizar um serviço web publicado do designer de Machine Learning do Azure.

**Usar o SDK**

O código a seguir mostra como usar o SDK para atualizar o modelo, o ambiente e o script de entrada de um serviço web:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Usando a CLI**

Você também pode atualizar um serviço web usando o ML CLI. O exemplo a seguir demonstra registrar um novo modelo e, em seguida, atualizar um serviço web para usar o novo modelo:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Neste exemplo, um documento JSON é usado para passar as informações do modelo do comando de registro para o comando update.
>
> Para atualizar o serviço para usar um novo script de entrada ou ambiente, crie um [arquivo de configuração de inferência](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) e especifique-o com o `ic` parâmetro.

Para obter mais informações, consulte a documentação [de atualização do serviço az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)