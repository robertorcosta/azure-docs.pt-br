---
title: Solução de problemas de implantação do Docker
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas, resolver e solucionar os erros comuns de implantação do Docker com o serviço kubernetes do Azure e as instâncias de contêiner do Azure usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: 259b5c789d2323dbc797116cf0d09045811a6873
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073335"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Solucionar problemas de implantação do Docker de modelos com o serviço kubernetes do Azure e instâncias de contêiner do Azure 

Saiba como solucionar problemas e resolver, ou contornar, erros comuns de implantação do Docker com ACI (instâncias de contêiner do Azure) e AKS (serviço kubernetes do Azure) usando Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação do Docker em funcionamento no sistema local.

    Para verificar a instalação do Docker, use o comando `docker run hello-world` em um terminal ou prompt de comando. Para obter informações sobre a instalação do Docker ou solução de erros do Docker, confira a [Documentação do Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Etapas para a implantação do Docker de modelos de aprendizado de máquina

Ao implantar um modelo no Azure Machine Learning, você usa a API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) e um objeto de [ambiente](how-to-use-environments.md) . O serviço cria uma imagem base do Docker durante o estágio de implantação e monta os modelos necessários em uma única chamada. As tarefas básicas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Definir configuração de inferência:
    1. Crie um objeto de [ambiente](how-to-use-environments.md) . Esse objeto pode usar as dependências em um arquivo de YAML de ambiente, um de nossos ambientes organizados.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Implante o modelo para o serviço da Instância de Contêiner do Azure (ACI) ou para o Serviço de Kubernetes do Azure (AKS).

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema.

Ao usar [Model. Deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) com um objeto de [ambiente](how-to-use-environments.md) como um parâmetro de entrada, seu código pode ser dividido em três etapas principais:

1. Registre o modelo. Estes são alguns exemplos de código:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definir configuração de inferência para implantação:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Implante o modelo usando a configuração de inferência criada na etapa anterior:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

A interrupção do processo de implantação de três em tarefas individuais facilita a identificação de alguns dos erros mais comuns.

## <a name="debug-locally"></a>Depurar Localmente

Se você tiver problemas ao implantar um modelo em ACI ou AKS, implante-o como um serviço Web local. Usar um serviço Web local facilita a solução de problemas.

Você pode encontrar um exemplo de [bloco de anotações de implantação local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) no repositório  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) para explorar uma amostra executável.

> [!WARNING]
> Não há suporte para implantações de serviço Web local para cenários de produção.

Para implantar localmente, modifique o código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implantação. Em seguida, use `Model.deploy()` para implantar o serviço. O exemplo a seguir implanta um modelo (contido na variável de modelo) como serviço Web local:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se você estiver definindo seu próprio Conda de especificação YAML, liste azureml-defaults versão >= 1.0.45 como uma dependência Pip. Esse pacote é necessário para hospedar o modelo como um serviço Web.

A essa altura, você pode trabalhar com o serviço normalmente. O código a seguir demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Para obter mais informações sobre como personalizar o ambiente do Python, confira [Criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md). 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, talvez seja necessário atualizar o arquivo `score.py` para adicionar o log ou tentar resolver os problemas detectados. Para recarregar as alterações no arquivo `score.py`, use `reload()`. Por exemplo, o código a seguir recarrega o script para o serviço e depois envia dados a ele. Os dados são pontuados usando o arquivo `score.py` atualizado:

> [!IMPORTANT]
> O método `reload` só está disponível para implantações locais. Para obter informações sobre como atualizar uma implantação para outro destino de computação, consulte [como atualizar seu WebService](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado no local especificado pelo objeto `InferenceConfig` usado pelo serviço.

Para alterar o modelo, as dependências Conda ou a configuração de implantação, use [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspecionar o log do Docker

Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços. Você pode exibir o log para implantações de ACI, AKS e local. O exemplo a seguir demonstra como fazer imprimir os logs.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Se você vir a linha que `Booting worker with pid: <pid>` está ocorrendo várias vezes nos logs, isso significa que não há memória suficiente para iniciar o trabalho.
Você pode resolver o erro aumentando o valor de `memory_gb` em `deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Não é possível agendar o contêiner

Ao implantar um serviço em um destino de computação do Serviço de Kubernetes do Azure, o Azure Machine Learning tentará agendar o serviço com a quantidade solicitada de recursos. Se não houver nós disponíveis no cluster com a quantidade apropriada de recursos após 5 minutos, a implantação falhará. A mensagem de falha é `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Você pode resolver esse erro adicionando mais nós, alterando a SKU de seus nós ou alterando os requisitos de recursos do seu serviço. 

A mensagem de erro normalmente indicará a qual recurso você precisa mais, por exemplo, se você vir uma mensagem de erro indicando `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` que significa que o serviço requer GPUs e que há três nós no cluster que não têm GPUs disponíveis. Isso pode ser resolvido adicionando mais nós, se você estiver usando um SKU de GPU, alternando para um SKU habilitado para GPU, se não estiver, ou alterando o ambiente para não exigir GPUs.  

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço

Depois que a imagem é criada com êxito, o sistema tenta iniciar um contêiner usando a configuração de implantação. Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro.

Use as informações na seção [Inspecionar o log do Docker](#dockerlog) para verificar os logs.

## <a name="function-fails-get_model_path"></a>Falha de função: get_model_path()

Geralmente, na função `init()` no script de pontuação, a função [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Se o arquivo ou pasta do modelo não puder ser encontrado, a função falhará. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Esse exemplo imprime o caminho local (relativo ao `/var/azureml-app`) no contêiner em que o seu script de pontuação está esperando encontrar o arquivo ou a pasta de modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de log como DEBUG poderá fazer com que as informações adicionais sobre a causa sejam registradas, o que podem ser útil para identificar a falha.

## <a name="function-fails-runinput_data"></a>Falha de função: run(input_data)

Se o serviço for implantado com êxito, mas falhar quando você publicar dados no ponto de extremidade de pontuação, você poderá adicionar o erro capturando instrução na função `run(input_data)` de modo que ele retorne a mensagem de erro detalhada em vez disso. Por exemplo:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Observação**: O retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Por motivos de segurança, você não deve retornar mensagens de erro dessa maneira em um ambiente de produção.

## <a name="http-status-code-502"></a>Código de status HTTP 502

Um código de status 502 indica que o serviço gerou uma exceção ou falhou no método `run()` do arquivo score.py. Use as informações neste artigo para depurar o arquivo.

## <a name="http-status-code-503"></a>Código de status HTTP 503

As implantações do Serviço de Kubernetes do Azure dão suporte ao dimensionamento automático, que permite que as réplicas sejam adicionadas para dar suporte à carga adicional. O dimensionador automático foi projetado para lidar com alterações **graduais** na carga. Se você receber grandes picos em solicitações por segundo, os clientes podem receber um código de status HTTP 503. Mesmo que o dimensionador reatue rapidamente, leva AKS uma quantidade significativa de tempo para criar contêineres adicionais.

As decisões de expansão/redução baseiam-se na utilização das réplicas de contêiner atuais. O número de réplicas ocupadas (processando uma solicitação) dividido pelo número total de réplicas atuais é a utilização atual. Se esse número exceder `autoscale_target_utilization` , mais réplicas serão criadas. Se for menor, as réplicas serão reduzidas. As decisões para adicionar réplicas são ansiosos e rápidas (cerca de 1 segundo). As decisões para remover réplicas são conservadoras (cerca de 1 minuto). Por padrão, a utilização de destino de dimensionamento automático é definida como **70%**, o que significa que o serviço pode lidar com picos em solicitações por segundo (RPS) de **até 30%**.

As duas ações a seguir podem evitar códigos de status 503:

> [!TIP]
> Essas duas abordagens podem ser usadas individualmente ou em combinação.

* Altere o nível de utilização no qual o dimensionamento automático cria novas réplicas. Você pode ajustar o destino de utilização, definindo o `autoscale_target_utilization` como um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, elas são criadas no limite inferior de utilização. Em vez de aguardar até que o serviço seja 70% utilizado, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorrer uma utilização de 30%.
    
    Se o serviço Web já estiver usando as réplicas máximas atuais e você ainda estiver vendo os códigos de status 503, aumente o valor `autoscale_max_replicas` para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` como um valor maior. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo os valores por valores específicos para o projeto:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Se você receber picos de solicitação maiores do que as novas réplicas mínimas podem suportar, você poderá receber códigos 503 novamente. Por exemplo, à medida que o tráfego para o serviço aumenta, talvez seja necessário aumentar as réplicas mínimas.

Para obter mais informações sobre como definir `autoscale_target_utilization`, `autoscale_max_replicas` e `autoscale_min_replicas`, confira a referência do módulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true).

## <a name="http-status-code-504"></a>Código de status HTTP 504

Um código de status 504 indica que a solicitação atingiu o tempo limite. O tempo limite padrão é 1 minuto.

Você pode aumentar o tempo limite ou tentar acelerar o serviço, modificando o score.py para remover as chamadas desnecessárias. Se essas ações não corrigirem o problema, use as informações neste artigo para depurar o arquivo score.py. O código pode estar em um estado sem resposta ou um loop infinito.

## <a name="advanced-debugging"></a>Depuração avançada

Talvez seja necessário depurar interativamente o código Python contido em sua implantação de modelo. Por exemplo, se o script de entrada estiver falhando e não for possível determinar o motivo por meio de um registro adicional. Usando Visual Studio Code e debugpy, você pode anexar ao código em execução dentro do contêiner do Docker.

Para obter mais informações, visite a [depuração interativa no guia vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Fórum do usuário de implantação de modelo](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
