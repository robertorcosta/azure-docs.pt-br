---
title: Criar ambientes ML reutilizáveis
titleSuffix: Azure Machine Learning
description: Crie e gerencie ambientes para treinamento e implantação de modelos. Gerenciar pacotes Python e outras configurações para o ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536345"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Reutilizar ambientes para treinamento e implantação usando o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a criar e gerenciar [ambientes](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)de Machine Learning do Azure. Use os ambientes para rastrear e reproduzir as dependências de software de seus projetos à medida que eles evoluem.

O gerenciamento de dependência de software é uma tarefa comum para desenvolvedores. Você deseja garantir que as compilações sejam reprodutíveis sem uma configuração manual extensiva do software. A classe Azure Machine Learning `Environment` conta com soluções de desenvolvimento local, como pip e Conda, e fornece uma solução para o desenvolvimento de nuvem local e distribuída.

Os exemplos deste artigo mostram como:

* Crie um ambiente e especifique dependências de pacotes.
* Recuperar e atualizar ambientes.
* Use um ambiente para treinamento.
* Use um ambiente para implantação de serviços web.

Para uma visão geral de alto nível de como os ambientes funcionam no Azure Machine Learning, veja [quais são os ambientes ML?](concept-environments.md)

## <a name="prerequisites"></a>Pré-requisitos

* O [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Um [espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Criar um ambiente

As seções a seguir exploram as múltiplas maneiras pelas quais você pode criar um ambiente para seus experimentos.

### <a name="use-a-curated-environment"></a>Use um ambiente curado

Você pode selecionar um dos ambientes curados para começar: 

* O _ambiente AzureML-Minimal_ contém um conjunto mínimo de pacotes para permitir o rastreamento de execução e o upload de ativos. Você pode usá-lo como ponto de partida para o seu próprio ambiente.

* O _ambiente AzureML-Tutorial_ contém pacotes comuns de ciência de dados. Esses pacotes incluem Scikit-Learn, Pandas, Matplotlib e um conjunto maior de pacotes azureml-sdk.

Os ambientes com curadoria são apoiados por imagens docker armazenadas em cache. Esse apoio reduz o custo de preparação da corrida.

Use `Environment.get` o método para selecionar um dos ambientes curados:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Você pode listar os ambientes curados e seus pacotes usando o seguinte código:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Não inicie seu próprio nome de ambiente com o prefixo _AzureML._ Este prefixo é reservado para ambientes curados.

### <a name="instantiate-an-environment-object"></a>Instanciar um objeto de ambiente

Para criar manualmente um `Environment` ambiente, importe a classe do SDK. Em seguida, use o código a seguir para instanciar um objeto do ambiente.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Use arquivos de especificação Conda e pip

Você também pode criar um ambiente a partir de uma especificação Conda ou de um arquivo de requisitos pip. Use [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) o método [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) ou o método. No argumento do método, inclua o nome do ambiente e o caminho do arquivo do arquivo que você deseja.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Use ambientes Conda existentes

Se você tem um ambiente Conda existente em seu computador local, então você pode usar o serviço para criar um objeto de ambiente. Usando essa estratégia, você pode reutilizar seu ambiente interativo local em corridas remotas.

O código a seguir cria um objeto `mycondaenv`de ambiente a partir do ambiente Conda existente . Usa o [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) método.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Crie ambientes automaticamente

Crie automaticamente um ambiente enviando uma corrida de treinamento. Envie a execução `submit()` usando o método. Quando você envia uma corrida de treinamento, a construção do novo ambiente pode levar vários minutos. A duração da construção depende do tamanho das dependências necessárias. 

Se você não especificar um ambiente na configuração de execução antes de enviar a execução, então um ambiente padrão será criado para você.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Da mesma forma, [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) se você usar um objeto para treinamento, você pode enviar diretamente a instância do estimador como uma execução sem especificar um ambiente. O `Estimator` objeto já encapsula o ambiente e o alvo da computação.

## <a name="add-packages-to-an-environment"></a>Adicionar pacotes a um ambiente

Adicione pacotes a um ambiente usando arquivos Conda, pip ou roda privada. Especifique a dependência [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) de cada pacote usando a classe. Adicione-o ao `PythonSection`ambiente.

### <a name="conda-and-pip-packages"></a>Pacotes Conda e pip

Se um pacote estiver disponível em um repositório de pacotes Conda, então recomendamos que você use a instalação do Conda em vez da instalação do pip. Os pacotes conda normalmente vêm com binários pré-construídos que tornam a instalação mais confiável.

O exemplo a seguir se soma ao meio ambiente. Ele adiciona a versão 1.17.0 de `numpy`. Ele também `pillow` adiciona `myenv`o pacote, . O exemplo [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) usa o [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) método e o método, respectivamente.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Se você usar a mesma definição de ambiente para outra execução, o serviço Azure Machine Learning reutiliza a imagem armazenada em cache do seu ambiente. Se você criar um ambiente com uma dependência ```numpy```de pacote não fixada, por exemplo, esse ambiente continuará usando a versão do pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição de correspondência continuará usando a versão antiga. Para obter mais informações, consulte [Construção de ambiente, cache e reutilização](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Arquivos de roda privada

Você pode usar arquivos de roda pip privados, primeiro carregando-os para o seu armazenamento no espaço de trabalho. Você os carrega usando [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) um método estático. Em seguida, você captura a URL `add_pip_package()` de armazenamento e passa a URL para o método.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gerenciar ambientes

Gerencie ambientes para que você possa atualizá-los, rastreá-los e reutilizá-los em alvos de computação e com outros usuários do espaço de trabalho.

### <a name="register-environments"></a>Registre ambientes

O ambiente é automaticamente registrado no seu espaço de trabalho quando você envia uma execução ou implanta um serviço web. Você também pode registrar manualmente [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) o ambiente usando o método. Esta operação torna o ambiente em uma entidade que é rastreada e versionada na nuvem. A entidade pode ser compartilhada entre usuários de espaço de trabalho.

O código a `myenv` seguir registra `ws` o ambiente no espaço de trabalho.

```python
myenv.register(workspace=ws)
```

Quando você usa o ambiente pela primeira vez em treinamento ou implantação, ele é registrado no espaço de trabalho. Então é construído e implantado no alvo da computação. O serviço armazena os ambientes. Reutilizar um ambiente em cache leva muito menos tempo do que usar um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obtenha ambientes existentes

A `Environment` classe oferece métodos que permitem recuperar ambientes existentes em seu espaço de trabalho. Você pode recuperar ambientes pelo nome, como uma lista ou por uma corrida de treinamento específica. Essas informações são úteis para solução de problemas, auditoria e reprodutibilidade.

#### <a name="view-a-list-of-environments"></a>Veja uma lista de ambientes

Veja os ambientes em seu [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) espaço de trabalho usando a classe. Em seguida, selecione um ambiente para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obtenha um ambiente pelo nome

Você também pode obter um ambiente específico por nome e versão. O código a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) seguir usa `1` o `myenv` método `ws` para recuperar a versão do ambiente no espaço de trabalho.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Treine um ambiente específico para execução

Para obter o ambiente que foi usado para uma corrida [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) específica `Run` após o término do treinamento, use o método na classe.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualize um ambiente existente

Digamos que você altere um ambiente existente, por exemplo, adicionando um pacote Python. Uma nova versão do ambiente é criada quando você envia uma execução, implanta um modelo ou registra manualmente o ambiente. A versão permite que você visualize as mudanças do ambiente ao longo do tempo.

Para atualizar uma versão do pacote Python em um ambiente existente, especifique o número da versão para esse pacote. Se você não usar o número exato da versão, então o Azure Machine Learning reutilizará o ambiente existente com suas versões originais do pacote.

### <a name="debug-the-image-build"></a>Depurar a compilação da imagem

O exemplo a [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) seguir usa o método para criar manualmente um ambiente como uma imagem Docker. Ele monitora os registros de saída [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)da compilação de imagem usando . Em seguida, a imagem incorporada aparece na instância do Registro de Contêineres Do Azure do espaço de trabalho. Essas informações são úteis para depuração.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Habilitar docker

 A [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) aula de Machine `Environment` Learning do Azure permite que você personalize e controle o sistema operacional convidado no qual você executa seu treinamento.

Quando você habilita o Docker, o serviço cria uma imagem do Docker. Ele também cria um ambiente Python que usa suas especificações dentro desse contêiner Docker. Essa funcionalidade oferece isolamento e reprodutibilidade adicionais para suas corridas de treinamento.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Por padrão, a imagem docker recém-construída aparece no registro de contêiner associado ao espaço de trabalho.  O nome do repositório tem o formulário *azureml/azureml_\<uuid\>*. A parte única do identificador *(uuid)* do nome corresponde a um hash que é calculado a partir da configuração do ambiente. Essa correspondência permite que o serviço determine se uma imagem para o determinado ambiente já existe para reutilização.

Além disso, o serviço usa automaticamente uma das [imagens base baseadas](https://github.com/Azure/AzureML-Containers)no Ubuntu Linux . Ele instala os pacotes Python especificados. A imagem base tem versões da CPU e gpu. O Azure Machine Learning detecta automaticamente qual versão usar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Você também pode especificar um arquivo Docker personalizado. É mais simples começar a partir de uma das imagens base do Azure Machine Learning usando o comando Docker ```FROM``` e, em seguida, adicionar seus próprios passos personalizados. Use essa abordagem se precisar instalar pacotes não-Python como dependências.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Use dependências gerenciadas pelo usuário

Em algumas situações, sua imagem base personalizada pode já conter um ambiente Python com pacotes que você deseja usar.

Por padrão, o serviço Azure Machine Learning construirá um ambiente Conda com dependências especificadas e executará a execução nesse ambiente em vez de usar quaisquer bibliotecas Python que você instalou na imagem base. 

Para usar seus próprios pacotes instalados, defina o parâmetro `Environment.python.user_managed_dependencies = True`. Certifique-se de que a imagem base contém um interpretador Python e tenha os pacotes que seu script de treinamento precisa.

Por exemplo, para ser executado em um ambiente Miniconda base que tenha o pacote NumPy instalado, primeiro especifique um arquivo Docker com uma etapa para instalar o pacote. Em seguida, defina as `True`dependências gerenciadas pelo usuário como . 

Você também pode especificar um caminho para um interpretador Python específico dentro da imagem, definindo a `Environment.python.interpreter_path` variável.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Use ambientes para treinamento

Para enviar uma corrida de treinamento, você precisa combinar seu ambiente, [objetivo de computação](concept-compute-target.md)e seu script Python de treinamento em uma configuração de execução. Esta configuração é um objeto de invólucro usado para enviar corridas.

Quando você envia uma corrida de treinamento, a construção de um novo ambiente pode levar vários minutos. A duração depende do tamanho das dependências necessárias. Os ambientes são armazenados em cache pelo serviço. Assim, enquanto a definição do ambiente permanecer inalterada, você incorre no tempo total de configuração apenas uma vez.

O exemplo de execução de [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) script local a seguir mostra onde você usaria como objeto de invólucro.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Para desativar o histórico de execução ou `ScriptRunConfig.run_config.history`executar instantâneos, use a configuração em .

Se você não especificar o ambiente na configuração de execução, o serviço criará um ambiente padrão ao enviar sua execução.

### <a name="use-an-estimator-for-training"></a>Use um estimador para treinamento

Se você usar um [estimador](how-to-train-ml-models.md) para treinamento, então você pode enviar a instância do estimador diretamente. Ele já encapsula o ambiente e o alvo da computação.

O código a seguir usa um estimador para uma corrida de treinamento de nó único. Ele é executado em um `scikit-learn` cálculo remoto para um modelo. Ele assume que você criou anteriormente um `compute_target`objeto de destino de `ds`computação, e um objeto de armazenamento de dados, .

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Use ambientes para implantação de serviços web

Você pode usar ambientes quando implantar seu modelo como um serviço web. Esse recurso permite um fluxo de trabalho conectado e reprodutível. Neste fluxo de trabalho, você pode treinar, testar e implantar seu modelo usando as mesmas bibliotecas tanto na sua computação de treinamento quanto na sua computação de inferência.

Para implantar um serviço web, combine o ambiente, a computação de inferência, o script de pontuação e o modelo registrado em seu objeto de implantação, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Para obter mais informações, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

Neste exemplo, suponha que você tenha completado uma corrida de treinamento. Agora você deseja implantar esse modelo no Azure Container Instances. Quando você constrói o serviço web, os arquivos de modelo e pontuação são montados na imagem, e a pilha de inferência de aprendizado de máquina do Azure é adicionada à imagem.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Este [exemplo de caderno](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expande-se sobre conceitos e métodos demonstrados neste artigo.

[Implantar um modelo usando uma imagem base docker personalizada](how-to-deploy-custom-docker-image.md) demonstra como implantar um modelo usando uma imagem base Docker personalizada.

Este [exemplo de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) demonstra como implantar um modelo Spark como um serviço web.

## <a name="create-and-manage-environments-with-the-cli"></a>Crie e gerencie ambientes com a CLI

O Cli de aprendizagem de [máquina do Azure](reference-azure-machine-learning-cli.md) espelha a maior parte da funcionalidade do Python SDK. Você pode usá-lo para criar e gerenciar ambientes. Os comandos que discutimos nesta seção demonstram funcionalidade básica.

O comando seguinte desata os arquivos para uma definição de ambiente padrão no diretório especificado. Estes arquivos são arquivos JSON. Eles trabalham como a classe correspondente no SDK. Você pode usar os arquivos para criar novos ambientes que tenham configurações personalizadas. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Execute o seguinte comando para registrar um ambiente a partir de um diretório especificado.

```azurecli-interactive
az ml environment register -d myenvdir
```

Execute o seguinte comando para listar todos os ambientes registrados.

```azurecli-interactive
az ml environment list
```

Baixe um ambiente registrado usando o seguinte comando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Próximas etapas

* Para usar um alvo de computação gerenciado para treinar um modelo, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md).
* Depois de ter um modelo treinado, aprenda [como e onde implantar modelos.](how-to-deploy-and-where.md)
* Exibir [ `Environment` a referência classe SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Para obter mais informações sobre os conceitos e métodos descritos neste artigo, consulte o [exemplo do caderno](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
