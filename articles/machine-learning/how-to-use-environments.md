---
title: Criar ambientes ML reutilizáveis
titleSuffix: Azure Machine Learning
description: Crie e gerencie ambientes para treinamento e implantação de modelo. Gerencie pacotes do Python e outras configurações para o ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536345"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Reutilizar ambientes para treinamento e implantação usando o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como criar e gerenciar [ambientes](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)de Azure Machine Learning. Use os ambientes para acompanhar e reproduzir as dependências de software de seus projetos à medida que elas evoluem.

O gerenciamento de dependências de software é uma tarefa comum para os desenvolvedores. Você quer garantir que as compilações sejam reproduzíveis sem uma ampla configuração manual de software. O Azure Machine Learning `Environment` contas de classe para soluções de desenvolvimento local, como PIP e Conda, e fornece uma solução para o desenvolvimento de nuvem local e distribuída.

Os exemplos neste artigo mostram como:

* Crie um ambiente e especifique as dependências do pacote.
* Recuperar e atualizar ambientes.
* Use um ambiente para treinamento.
* Use um ambiente para implantação de serviço Web.

Para obter uma visão geral de alto nível de como os ambientes funcionam no Azure Machine Learning, consulte [o que são ambientes de ml?](concept-environments.md).

## <a name="prerequisites"></a>Pré-requisitos

* O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Criar um ambiente

As seções a seguir exploram várias maneiras pelas quais você pode criar um ambiente para seus experimentos.

### <a name="use-a-curated-environment"></a>Usar um ambiente organizado

Você pode selecionar um dos ambientes organizados para começar: 

* O ambiente do _AzureML-mínimo_ contém um conjunto mínimo de pacotes para habilitar o acompanhamento de execução e o carregamento de ativos. Você pode usá-lo como um ponto de partida para seu próprio ambiente.

* O ambiente do _AzureML-tutorial_ contém pacotes comuns de ciência de dados. Esses pacotes incluem o Scikit-learn, o pandas, o matplotlib e um conjunto maior de pacotes do azureml-SDK.

Os ambientes organizados são apoiados por imagens do Docker armazenadas em cache. Esse backup reduz o custo de preparação da execução.

Use o `Environment.get` método para selecionar um dos ambientes organizados:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Você pode listar os ambientes organizados e seus pacotes usando o seguinte código:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Não inicie seu próprio nome de ambiente com o prefixo do _AzureML_ . Esse prefixo é reservado para ambientes organizados.

### <a name="instantiate-an-environment-object"></a>Criar uma instância de um objeto de ambiente

Para criar um ambiente manualmente, importe a `Environment` classe do SDK. Em seguida, use o código a seguir para criar uma instância de um objeto de ambiente.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Usar arquivos de especificação Conda e Pip

Você também pode criar um ambiente de uma especificação Conda ou um arquivo de requisitos Pip. Use o [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) método ou o [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) método. No argumento do método, inclua o nome do ambiente e o caminho do arquivo desejado.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Usar ambientes Conda existentes

Se você tiver um ambiente Conda existente no computador local, poderá usar o serviço para criar um objeto de ambiente. Usando essa estratégia, você pode reutilizar seu ambiente interativo local em execuções remotas.

O código a seguir cria um objeto de ambiente do ambiente `mycondaenv`Conda existente. Ele usa o [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) método.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Criar ambientes automaticamente

Crie um ambiente automaticamente enviando uma execução de treinamento. Envie a execução usando o `submit()` método. Quando você envia uma execução de treinamento, a criação do novo ambiente pode levar vários minutos. A duração da compilação depende do tamanho das dependências necessárias. 

Se você não especificar um ambiente em sua configuração de execução antes de enviar a execução, um ambiente padrão será criado para você.

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

Da mesma forma, se você [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) usar um objeto para treinamento, poderá enviar diretamente a instância do estimador como uma execução sem especificar um ambiente. O `Estimator` objeto já encapsula o ambiente e o destino de computação.

## <a name="add-packages-to-an-environment"></a>Adicionar pacotes a um ambiente

Adicione pacotes a um ambiente usando arquivos Conda, Pip ou de roda privada. Especifique cada dependência do pacote usando a [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) classe. Adicione-o ao ambiente do `PythonSection`.

### <a name="conda-and-pip-packages"></a>Pacotes Conda e Pip

Se um pacote estiver disponível em um repositório de pacotes do Conda, recomendamos que você use a instalação do Conda em vez da instalação do Pip. Os pacotes Conda normalmente vêm com binários predefinidos que tornam a instalação mais confiável.

O exemplo a seguir adiciona ao ambiente. Ele adiciona a versão 1.17.0 `numpy`do. Ele também adiciona o `pillow` pacote, `myenv`. O exemplo usa o [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) método e o [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) método, respectivamente.

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
> Se você usar a mesma definição de ambiente para outra execução, o serviço de Azure Machine Learning reutiliza a imagem armazenada em cache do seu ambiente. Se você criar um ambiente com uma dependência de pacote desafixada, ```numpy```por exemplo, esse ambiente continuará usando a versão do pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará usando a versão antiga. Para obter mais informações, consulte [criação de ambiente, Caching e reutilização](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Arquivos de roda particulares

Você pode usar arquivos de roda Pip privados primeiro carregando-os no armazenamento do espaço de trabalho. Você os carrega usando um método estático [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) . Em seguida, você captura a URL de armazenamento e passa a `add_pip_package()` URL para o método.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gerenciar ambientes

Gerencie ambientes para que você possa atualizá-los, rastreá-los e reutilizá-los em destinos de computação e com outros usuários do espaço de trabalho.

### <a name="register-environments"></a>Registrar ambientes

O ambiente é registrado automaticamente com seu espaço de trabalho quando você envia uma execução ou implanta um serviço Web. Você também pode registrar manualmente o ambiente usando o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) método. Essa operação torna o ambiente em uma entidade que é rastreada e com controle de versão na nuvem. A entidade pode ser compartilhada entre os usuários do espaço de trabalho.

O código a seguir registra `myenv` o ambiente no `ws` espaço de trabalho.

```python
myenv.register(workspace=ws)
```

Quando você usa o ambiente pela primeira vez em treinamento ou implantação, ele é registrado com o espaço de trabalho. Em seguida, ele é compilado e implantado no destino de computação. O serviço armazena em cache os ambientes. Reutilizar um ambiente armazenado em cache leva muito menos tempo do que usar um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obter ambientes existentes

A `Environment` classe oferece métodos que permitem que você recupere ambientes existentes em seu espaço de trabalho. Você pode recuperar ambientes por nome, como uma lista ou por uma execução de treinamento específica. Essas informações são úteis para solução de problemas, auditoria e reprodução.

#### <a name="view-a-list-of-environments"></a>Exibir uma lista de ambientes

Exiba os ambientes em seu espaço de trabalho usando [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) a classe. Em seguida, selecione um ambiente para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obter um ambiente por nome

Você também pode obter um ambiente específico por nome e versão. O código a seguir usa [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) o método para recuperar `1` a versão `myenv` do ambiente no `ws` espaço de trabalho.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Treinar um ambiente específico de execução

Para obter o ambiente que foi usado para uma execução específica após a conclusão do treinamento, use [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) o método na `Run` classe.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualizar um ambiente existente

Digamos que você altere um ambiente existente, por exemplo, adicionando um pacote do Python. Uma nova versão do ambiente é criada quando você envia uma execução, implanta um modelo ou registra manualmente o ambiente. O controle de versão permite que você exiba as alterações do ambiente ao longo do tempo.

Para atualizar uma versão de pacote do Python em um ambiente existente, especifique o número de versão para esse pacote. Se você não usar o número de versão exato, Azure Machine Learning irá reutilizar o ambiente existente com suas versões de pacote originais.

### <a name="debug-the-image-build"></a>Depurar a compilação da imagem

O exemplo a seguir usa [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) o método para criar manualmente um ambiente como uma imagem do Docker. Ele monitora os logs de saída da compilação da imagem usando [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)o. A imagem interna é exibida na instância do registro de contêiner do Azure do espaço de trabalho. Essas informações são úteis para depuração.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Habilitar Docker

 O [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) da classe Azure Machine Learning `Environment` permite que você personalize e controle com detalhes o sistema operacional convidado no qual você executa o treinamento.

Quando você habilita o Docker, o serviço cria uma imagem do Docker. Ele também cria um ambiente Python que usa suas especificações dentro desse contêiner do Docker. Essa funcionalidade fornece isolamento e reprodução adicionais para suas execuções de treinamento.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Por padrão, a imagem do Docker criada recentemente aparece no registro de contêiner associado ao espaço de trabalho.  O nome do repositório tem o formato *azureml/\<azureml_\>UUID*. A parte do identificador exclusivo (*UUID*) do nome corresponde a um hash calculado a partir da configuração do ambiente. Essa correspondência permite que o serviço determine se uma imagem para o ambiente fornecido já existe para reutilização.

Além disso, o serviço usa automaticamente uma das [imagens base](https://github.com/Azure/AzureML-Containers)baseadas em Ubuntu Linux. Ele instala os pacotes do Python especificados. A imagem base tem versões de CPU e de GPU. Azure Machine Learning detecta automaticamente qual versão usar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Você também pode especificar um Dockerfile personalizado. É mais simples iniciar a partir de uma das Azure Machine Learning imagens base usando o ```FROM``` comando Docker e, em seguida, adicionar suas próprias etapas personalizadas. Use essa abordagem se você precisar instalar pacotes não Python como dependências.

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

### <a name="use-user-managed-dependencies"></a>Usar dependências gerenciadas pelo usuário

Em algumas situações, sua imagem de base personalizada pode já conter um ambiente Python com pacotes que você deseja usar.

Por padrão, Azure Machine Learning serviço criará um ambiente Conda com dependências especificadas e executará a execução nesse ambiente em vez de usar as bibliotecas do Python que você instalou na imagem base. 

Para usar seus próprios pacotes instalados, defina o parâmetro `Environment.python.user_managed_dependencies = True`. Verifique se a imagem base contém um intérprete Python e tem os pacotes de que seu script de treinamento precisa.

Por exemplo, para executar em um ambiente Miniconda básico que tem o pacote NumPy instalado, primeiro especifique um Dockerfile com uma etapa para instalar o pacote. Em seguida, defina as dependências gerenciadas pelo usuário para `True`. 

Você também pode especificar um caminho para um intérprete Python específico dentro da imagem, definindo a `Environment.python.interpreter_path` variável.

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

## <a name="use-environments-for-training"></a>Usar ambientes para treinamento

Para enviar uma execução de treinamento, você precisa combinar seu ambiente, o [destino de computação](concept-compute-target.md)e o script Python de treinamento em uma configuração de execução. Essa configuração é um objeto wrapper que é usado para enviar execuções.

Quando você envia uma execução de treinamento, a criação de um novo ambiente pode levar vários minutos. A duração depende do tamanho das dependências necessárias. Os ambientes são armazenados em cache pelo serviço. Assim, desde que a definição do ambiente permaneça inalterada, você incorrerá no tempo de configuração completa apenas uma vez.

O exemplo de execução de script local a seguir mostra onde [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) você usaria como seu objeto wrapper.

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
> Para desabilitar o histórico de execução ou executar instantâneos, use a configuração `ScriptRunConfig.run_config.history`em.

Se você não especificar o ambiente em sua configuração de execução, o serviço criará um ambiente padrão quando você enviar sua execução.

### <a name="use-an-estimator-for-training"></a>Usar um estimador para treinamento

Se você usar um [estimador](how-to-train-ml-models.md) para treinamento, poderá enviar a instância do estimador diretamente. Ele já encapsula o ambiente e o destino de computação.

O código a seguir usa um estimador para uma execução de treinamento de nó único. Ele é executado em uma computação remota para `scikit-learn` um modelo. Ele pressupõe que você criou anteriormente um objeto de destino de `compute_target`computação, e um objeto de armazenamento `ds`de datastore,.

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

## <a name="use-environments-for-web-service-deployment"></a>Usar ambientes para implantação de serviço Web

Você pode usar ambientes ao implantar seu modelo como um serviço Web. Essa funcionalidade permite um fluxo de trabalho reproduzível e conectado. Neste fluxo de trabalho, você pode treinar, testar e implantar seu modelo usando as mesmas bibliotecas em sua computação de treinamento e sua computação de inferência.

Para implantar um serviço Web, combine o ambiente, a computação de inferência, o script de Pontuação e o modelo registrado [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)em seu objeto de implantação,. Para obter mais informações, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

Neste exemplo, suponha que você concluiu uma execução de treinamento. Agora você deseja implantar esse modelo nas instâncias de contêiner do Azure. Quando você cria o serviço Web, os arquivos de modelo e de pontuação são montados na imagem e a pilha de inferência de Azure Machine Learning é adicionada à imagem.

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

Este [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expande sobre conceitos e métodos demonstrados neste artigo.

[Implantar um modelo usando uma imagem de base do Docker personalizada](how-to-deploy-custom-docker-image.md) demonstra como implantar um modelo usando uma imagem de base do Docker personalizada.

Este [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) demonstra como implantar um modelo Spark como um serviço Web.

## <a name="create-and-manage-environments-with-the-cli"></a>Criar e gerenciar ambientes com a CLI

A [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) espelha a maior parte da funcionalidade do SDK do Python. Você pode usá-lo para criar e gerenciar ambientes. Os comandos que discutimos nesta seção demonstram a funcionalidade básica.

O comando a seguir aplica Scaffold os arquivos para uma definição de ambiente padrão no diretório especificado. Esses arquivos são arquivos JSON. Eles funcionam como a classe correspondente no SDK. Você pode usar os arquivos para criar novos ambientes com configurações personalizadas. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Execute o comando a seguir para registrar um ambiente de um diretório especificado.

```azurecli-interactive
az ml environment register -d myenvdir
```

Execute o comando a seguir para listar todos os ambientes registrados.

```azurecli-interactive
az ml environment list
```

Baixe um ambiente registrado usando o comando a seguir.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Próximas etapas

* Para usar um destino de computação gerenciado para treinar um modelo, consulte [tutorial: treinar um modelo](tutorial-train-models-with-aml.md).
* Depois de ter um modelo treinado, saiba [como e onde implantar modelos](how-to-deploy-and-where.md).
* Exiba a [ `Environment` referência do SDK de classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Para obter mais informações sobre os conceitos e métodos descritos neste artigo, consulte o [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
