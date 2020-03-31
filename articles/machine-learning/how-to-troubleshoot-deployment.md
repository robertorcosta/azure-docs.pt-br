---
title: Guia de solução de problemas de implantação
titleSuffix: Azure Machine Learning
description: Aprenda a contornar, resolver e solucionar problemas com os erros comuns de implantação do Docker com o Azure Kubernetes Service e o Azure Container Instances usando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399674"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Solução de problemas Azure Machine Learning Azure Kubernetes Service e Azure Container Instances implantação

Aprenda a contornar ou resolver erros comuns de implantação do Docker com a ACI (ACI) e o Azure Kubernetes Service (AKS) usando o Azure Machine Learning.

Ao implantar um modelo no Azure Machine Learning, o sistema executa uma série de tarefas.

A abordagem recomendada e mais atualizada para a implantação do modelo é através da API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) usando um objeto [Ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como parâmetro de entrada. Neste caso, nosso serviço criará uma imagem de docker base para você durante a fase de implantação e montará os modelos necessários tudo em uma chamada. As tarefas básicas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Definir configuração de inferência:
    1. Crie um objeto [Ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) com base nas dependências especificadas no arquivo yaml ambiente ou use um de nossos ambientes adquiridos.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Implante o modelo para o serviço Azure Container Instance (ACI) ou para o Azure Kubernetes Service (AKS).

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure.** Se você não tiver um, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* A [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [extensão CLI para Aprendizado de Máquina do Azure](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação docker funcionando em seu sistema local.

    Para verificar a instalação do `docker run hello-world` Docker, use o comando de um terminal ou solicitação de comando. Para obter informações sobre a instalação do Docker ou a solução de problemas do Docker, consulte a [Documentação do Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema.

Supondo que você esteja usando o novo/recomendado método de implantação via API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) com um objeto [De ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como parâmetro de entrada, seu código pode ser dividido em três etapas principais:

1. Registre o modelo. Aqui está um código de exemplo:

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

3. Implantar o modelo usando a configuração de inferência criada na etapa anterior:

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

Depois de termos dividido o processo de implantação em tarefas individuais, podemos ver alguns dos erros mais comuns.

## <a name="debug-locally"></a>Depurar Localmente

Se você encontrar problemas para implantar um modelo na ACI ou AKS, tente implantá-lo como um serviço web local. O uso de um serviço web local facilita a solução de problemas. A imagem do Docker contendo o modelo é baixada e iniciada em seu sistema local.

> [!WARNING]
> As implantações locais de serviços web não são suportadas para cenários de produção.

Para implantar localmente, modifique `LocalWebservice.deploy_configuration()` seu código para usar para criar uma configuração de implantação. Em `Model.deploy()` seguida, use para implantar o serviço. O exemplo a seguir implanta um modelo (contido na variável modelo) como um serviço web local:

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

Observe que se você estiver definindo sua própria especificação de conda YAML, você deve listar azureml-defaults com a versão >= 1.0.45 como uma dependência pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Neste ponto, você pode trabalhar com o serviço normalmente. Por exemplo, o código a seguir demonstra o envio de dados para o serviço:

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

Para obter mais informações sobre a personalização do ambiente Python, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md) 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, você `score.py` pode precisar atualizar o arquivo para adicionar registro ou tentar resolver quaisquer problemas que você tenha descoberto. Para recarregar alterações `score.py` no `reload()`arquivo, use . Por exemplo, o código a seguir recarrega o script para o serviço e, em seguida, envia dados para ele. Os dados são pontuados usando o arquivo atualizado: `score.py`

> [!IMPORTANT]
> O `reload` método só está disponível para implantações locais. Para obter informações sobre como atualizar uma implantação para outro alvo de computação, consulte a seção de atualização dos [modelos Deploy](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado do local `InferenceConfig` especificado pelo objeto usado pelo serviço.

Para alterar o modelo, as dependências do Conda ou a configuração de implantação, use [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Inspecione o registro do Docker

Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços. Você pode visualizar o registro de implantações ACI, AKS e Local. O exemplo a seguir demonstra como imprimir os registros.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço

Depois que a imagem é construída com sucesso, o sistema tenta iniciar um contêiner usando sua configuração de implantação. Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro.

Use as informações na seção [Inspecionar o registro do Docker](#dockerlog) para verificar os registros.

## <a name="function-fails-get_model_path"></a>Falha de função: get_model_path()

Muitas vezes, `init()` na função no script de pontuação, a função [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) é chamada para localizar um arquivo modelo ou uma pasta de arquivos de modelo no recipiente. Se o arquivo ou pasta do modelo não puder ser encontrado, a função falhará. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho `/var/azureml-app`local (em relação a ) no contêiner onde seu script de pontuação espera encontrar o arquivo ou pasta do modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de registro como DEBUG pode fazer com que informações adicionais sejam registradas, o que pode ser útil para identificar a falha.

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

**Observação**: o retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Por razões de segurança, você não deve retornar mensagens de erro desta forma em um ambiente de produção.

## <a name="http-status-code-502"></a>Código de status HTTP 502

Um código de status 502 indica que o `run()` serviço lançou uma exceção ou caiu no método do arquivo score.py. Use as informações neste artigo para depurar o arquivo.

## <a name="http-status-code-503"></a>Código de status HTTP 503

As implantações do Azure Kubernetes Service suportam o autoscaling, que permite que réplicas sejam adicionadas para suportar carga adicional. No entanto, o autoscaler foi projetado para lidar com mudanças **graduais** na carga. Se você receber grandes picos de solicitações por segundo, os clientes podem receber um código de status HTTP 503.

Existem duas coisas que podem ajudar a prevenir 503 códigos de status:

* Altere o nível de utilização no qual o autoscaling cria novas réplicas.
    
    Por padrão, a utilização da meta de dimensionamento automático é definida para 70%, o que significa que o serviço pode lidar com picos de solicitações por segundo (RPS) de até 30%. Você pode ajustar a meta `autoscale_target_utilization` de utilização definindo o valor para um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, eles são criados em um limite de utilização menor. Em vez de esperar até que o serviço seja utilizado em 70%, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorre 30% de aproveitamento.
    
    Se o serviço web já estiver usando as réplicas máximas atuais e `autoscale_max_replicas` você ainda estiver vendo 503 códigos de status, aumente o valor para aumentar o número máximo de réplicas.

* Alterar o número mínimo de réplicas. Aumentar as réplicas mínimas fornece uma piscina maior para lidar com os picos de entrada.

    Para aumentar o número mínimo `autoscale_min_replicas` de réplicas, defina para um valor mais alto. Você pode calcular as réplicas necessárias usando o seguinte código, substituindo valores por valores específicos do seu projeto:

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
    > Se você receber picos de solicitação maiores do que as novas réplicas mínimas podem suportar, você pode receber 503s novamente. Por exemplo, à medida que o tráfego para o seu serviço aumenta, você pode precisar aumentar as réplicas mínimas.

Para obter mais `autoscale_target_utilization` `autoscale_max_replicas`informações `autoscale_min_replicas` sobre como definir e para, consulte a referência do módulo [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Código de status HTTP 504

Um código de status 504 indica que a solicitação foi cronometrada. O tempo de intervalo padrão é de 1 minuto.

Você pode aumentar o tempo limite ou tentar acelerar o serviço modificando o score.py para remover chamadas desnecessárias. Se essas ações não corrigirem o problema, use as informações deste artigo para depurar o arquivo score.py. O código pode estar em um estado pendurado ou em um loop infinito.

## <a name="advanced-debugging"></a>Depuração avançada

Em alguns casos, você pode precisar depurar interativamente o código Python contido na implantação do seu modelo. Por exemplo, se o script de entrada estiver falhando e a razão não puder ser determinada por registro adicional. Usando o Visual Studio Code e o Python Tools for Visual Studio (PTVSD), você pode anexar ao código que está sendo executado dentro do contêiner Docker.

> [!IMPORTANT]
> Este método de depuração não `Model.deploy()` `LocalWebservice.deploy_configuration` funciona ao usar e implantar um modelo localmente. Em vez disso, você deve criar uma imagem usando o método [Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

As implantações locais de serviços web exigem uma instalação do Docker em funcionamento em seu sistema local. Para obter mais informações sobre o uso do Docker, consulte a [Documentação do Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o Python Tools for Visual Studio (PTVSD) no ambiente local de desenvolvimento do VS Code, use o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como usar PTVSD com código VS, consulte [Depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o CÓDIGO VS para se comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __Depuração__ e selecione __Configurações abertas__. Um arquivo chamado __launch.json__ é aberto.

    1. No arquivo __launch.json,__ encontre a `"configurations": [`linha que contém e insira o seguinte texto após ele:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na seção de configurações, adicione uma comuma (,) após o código inserido.

        Esta seção se anexa ao contêiner Docker usando a porta 5678.

    1. Salve o arquivo __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Crie uma imagem que inclua PTVSD

1. Modifique o ambiente conda para sua implantação para que inclua PTVSD. O exemplo a seguir `pip_packages` demonstra adicioná-lo usando o parâmetro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para iniciar o PTVSD e esperar por uma conexão quando `score.py` o serviço for iniciado, adicione o seguinte à parte superior do seu arquivo:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Crie uma imagem com base na definição do ambiente e puxe a imagem para o registro local. Durante a depuração, você pode querer fazer alterações nos arquivos da imagem sem ter que recriá-lo. Para instalar um editor de texto (vim) `Environment.docker.base_image` `Environment.docker.base_dockerfile` na imagem do Docker, use as propriedades e:

    > [!NOTE]
    > Este exemplo assume `ws` que aponta para o seu espaço `model` de trabalho de Machine Learning do Azure, e esse é o modelo que está sendo implantado. O `myenv.yml` arquivo contém as dependências de conda criadas na etapa 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Uma vez que a imagem tenha sido criada e baixada, o caminho da imagem (inclui repositório, nome e tag, que neste caso também é seu digesto) é exibido em uma mensagem semelhante à seguinte:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Para facilitar o trabalho com a imagem, use o seguinte comando para adicionar uma tag. Substitua pelo `myimagepath` valor de localização da etapa anterior.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o resto das etapas, você pode `debug:1` consultar a imagem local como em vez do valor completo do caminho da imagem.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se você definir um tempo para a conexão PTVSD no `score.py` arquivo, você deve conectar o CÓDIGO VS à sessão de depuração antes que o tempo expirar. Inicie o CÓDIGO VS, `score.py`abra a cópia local de , defina um ponto de ruptura e prepare-o antes de usar as etapas nesta seção.
>
> Para obter mais informações sobre depuração e configuração de pontos de interrupção, consulte [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um contêiner Docker usando a imagem, use o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para anexar o código VS ao PTVSD dentro do recipiente, abra o CÓDIGO VS e use a tecla F5 ou selecione __Debug__. Quando solicitado, selecione a configuração __Azure Machine Learning: Docker Debug.__ Você também pode selecionar o ícone de depuração na barra lateral, a entrada __Azure Machine Learning: Docker Debug__ no menu suspenso Debug e, em seguida, usar o arqueiro verde para anexar o depurador.

    ![O ícone de depuração, o botão de depuração iniciar e o seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, o VS Code se conecta ao PTVSD dentro do contêiner Docker e pára no ponto de ruptura que você define anteriormente. Agora você pode passar pelo código enquanto ele é executado, ver variáveis, etc.

Para obter mais informações sobre o uso do CÓDIGO VS para depurar python, consulte [Debug seu código Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modifique os arquivos do contêiner

Para fazer alterações nos arquivos na imagem, você pode anexar ao contêiner em execução e executar um shell bash. A partir daí, você pode usar vim para editar arquivos:

1. Para conectar-se ao recipiente em execução e lançar uma concha de bash no recipiente, use o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para encontrar os arquivos usados pelo serviço, use o seguinte comando do shell `/var/azureml-app`bash no contêiner se o diretório padrão for diferente de :

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, você pode `score.py` usar vim para editar o arquivo. Para obter mais informações sobre como usar vim, consulte [Usando o editor Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. As alterações em um recipiente normalmente não são persistidas. Para salvar quaisquer alterações que você fizer, use o seguinte comando, antes de sair do shell iniciado na etapa acima (ou seja, em outra concha):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma `debug:2` nova imagem chamada que contém suas edições.

    > [!TIP]
    > Você precisará parar o recipiente atual e começar a usar a nova versão antes que as alterações entrem em vigor.

1. Certifique-se de manter as alterações que você faz em arquivos no contêiner em sincronia com os arquivos locais que o VS Code usa. Caso contrário, a experiência de depurador não funcionará como esperado.

### <a name="stop-the-container"></a>Parar o contêiner

Para parar o contêiner, use o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar & implantar modelos](tutorial-train-models-with-aml.md)
