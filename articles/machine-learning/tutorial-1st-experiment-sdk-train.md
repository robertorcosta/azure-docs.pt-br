---
title: 'Tutorial: Treinar seu primeiro modelo de machine learning – Python'
titleSuffix: Azure Machine Learning
description: A parte 3 da série de introdução do Azure Machine Learning mostra como treinar um modelo de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: f5c2690ea97136c2b7887a8450c2788e3902d4e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369953"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutorial: Treinar seu primeiro modelo de machine learning (parte 3 de 4)

Este tutorial mostra como treinar um modelo de machine learning no Azure Machine Learning.

Este tutorial é a *parte 3 de uma série de tutoriais de quatro partes* em que você aprenderá os conceitos básicos do Azure Machine Learning e realizará tarefas de machine learning baseadas em trabalhos no Azure. Ele se baseia no trabalho que você concluiu na [Parte 1: Configurar](tutorial-1st-experiment-sdk-setup-local.md) e [Parte 2: Executar um script "Olá, Mundo!"](tutorial-1st-experiment-hello-world.md) da série.

Neste tutorial, você seguirá a próxima etapa enviando um script que treina um modelo de machine learning. Este exemplo ajudará você a entender como o Azure Machine Learning facilita o comportamento consistente entre a depuração local e as execuções remotas.

Neste tutorial, você:

> [!div class="checklist"]
> * Crie um script de treinamento.
> * Usa o Conda para definir um ambiente do Azure Machine Learning.
> * Cria um script de controle.
> * Aprende os conceitos básicos das classes do Azure Machine Learning (`Environment`, `Run`, `Metrics`).
> * Envia e executa seu script de treinamento.
> * Exibe a saída do código na nuvem.
> * Registra em log as métricas para o Azure Machine Learning.
> * Exibe suas métricas na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [parte 2](tutorial-1st-experiment-hello-world.md) da série.
* Conhecimento introdutório da linguagem Python e dos fluxos de trabalho de machine learning.
* Ambiente de desenvolvimento local, como o Visual Studio Code, o Jupyter ou o PyCharm.
* Python (versão 3.5 a 3.7).

## <a name="create-training-scripts"></a>Criar scripts de treinamento

Primeiro, você define a arquitetura de rede neural em um arquivo `model.py`. Todo o seu código de treinamento entrará no subdiretório `src`, incluindo `model.py`.

O código a seguir é obtido [deste exemplo introdutório](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) do PyTorch. Observe que os conceitos do Azure Machine Learning aplicam-se a qualquer código de machine learning, não apenas ao PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Em seguida, você define o script de treinamento. Esse script baixa o conjunto de dados CIFAR10 usando as APIs `torchvision.dataset` do PyTorch, configura a rede definida em `model.py` e a treina por duas épocas usando SGD padrão e perda de entropia cruzada.

Crie um script de `train.py` no subdiretório `src`:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Agora você tem a seguinte estrutura de diretório:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="create-a-python-environment"></a>Criar um ambiente de Python

Para fins de demonstração, vamos usar um ambiente Conda. (As etapas de um ambiente virtual Pip são quase idênticas.)

Crie um arquivo chamado `pytorch-env.yml` no diretório `.azureml` oculto:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Esse ambiente tem todas as dependências que o seu modelo e o script de treinamento exigem. Observe que não há nenhuma dependência do SDK do Azure Machine Learning para Python.

## <a name="test-locally"></a>Testar localmente

Use o seguinte código para testar as execuções de script localmente nesse ambiente:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Depois de executar esse script, você verá os dados baixados em um diretório chamado `tutorial/data`.

## <a name="create-the-control-script"></a>Criar o script de controle

A diferença entre o script de controle a seguir e aquele que você usou para enviar o script "Olá, Mundo!" é que você adiciona algumas linhas extras para definir o ambiente.

Crie um arquivo Python no diretório `tutorial` chamado `04-run-pytorch.py`:

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Entender as alterações de código

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      O Azure Machine Learning fornece o conceito de [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) para representar um ambiente reproduzível com controle de versão do Python para a execução de experimentos. É fácil criar um ambiente de um ambiente Conda ou pip local.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Adiciona o ambiente a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Enviar a execução para o Azure Machine Learning

Se você alternou os ambientes locais, lembre-se de voltar para um ambiente que tenha o SDK do Azure Machine Learning para Python instalado. 

Em seguida, execute:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Na primeira vez que você executar esse script, o Azure Machine Learning criará uma imagem do Docker por meio do ambiente PyTorch. A execução inteira pode levar de 5 a 10 minutos para ser concluída. 
>
> Veja os logs de build do Docker no Azure Machine Learning Studio. Siga o link para o estúdio, selecione a guia **Saídas + logs** e escolha `20_image_build_log.txt`.
>
> Essa imagem será reutilizada em execuções futuras para tornar a execução muito mais rápida.

Depois que a imagem for criada, selecione `70_driver_log.txt` para ver a saída do script de treinamento.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Se um erro `Your total snapshot size exceeds the limit` for exibido, isso indicará que o diretório `data` estará localizado no valor `source_directory` usado em `ScriptRunConfig`.
>
> Mova `data` para fora de `src`.

Os ambientes podem ser registrados em um workspace com `env.register(ws)`. Em seguida, eles podem ser facilmente compartilhados, reutilizados e ter o controle de versão. Os ambientes facilitam a reprodução de resultados anteriores e a colaboração com a equipe.

O Azure Machine Learning também mantém uma coleção de ambientes organizados. Esses ambientes abrangem cenários comuns de machine learning e são apoiados por imagens do Docker armazenadas em cache. As imagens do Docker armazenadas em cache aceleram a primeira execução remota.

Em suma, o uso de ambientes registrados pode poupar tempo! Leia [Como usar ambientes](./how-to-use-environments.md) para obter mais informações.

## <a name="log-training-metrics"></a>Métricas de treinamento de log

Agora que você tem um treinamento de modelo no Azure Machine Learning, comece a controlar algumas métricas de desempenho.

O script de treinamento atual imprime as métricas para o terminal. O Azure Machine Learning fornece um mecanismo para registrar métricas em log com mais funcionalidade. Ao adicionar algumas linhas de código, você pode visualizar as métricas no estúdio e compará-las entre várias execuções.

### <a name="modify-trainpy-to-include-logging"></a>Modifique `train.py` para incluir o registro em log

Modifique o script `train.py` para incluir mais duas linhas de código:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Entender as duas linhas de código adicionais

Em `train.py`, você acessa o objeto de execução _dentro_ do próprio script de treinamento usando o método `Run.get_context()` e o usa para registrar métricas:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

As métricas no Azure Machine Learning são:

- Organizadas por experimento e execução, sendo, portanto, fácil controlar e comparar métricas.
- Equipado com uma interface do usuário para que você possa visualizar o desempenho de treinamento no estúdio.
- Projetado para ajuste de escala, assim, você aproveita esses benefícios mesmo quando executa centenas de experimentos.

### <a name="update-the-conda-environment-file"></a>Atualizar o arquivo do ambiente Conda

O script `train.py` acabou de receber uma nova dependência de `azureml.core`. Atualize `pytorch-env.yml` para refletir essa alteração:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Enviar a execução para o Azure Machine Learning
Envie este script mais uma vez:

```bash
python 04-run-pytorch.py
```

Desta vez, quando você visitar o estúdio, acesse a guia **Métricas**, na qual agora poderá ver atualizações dinâmicas sobre a perda de treinamento do modelo.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Grafo de perda de treinamento na guia Métricas.":::

## <a name="next-steps"></a>Próximas etapas

Nesta sessão, você atualizou de um script "Olá, Mundo!" básico para um script de treinamento mais realista que exigia a execução de um ambiente específico do Python. Você viu como levar um ambiente Conda local para a nuvem com ambientes do Azure Machine Learning. Por fim, em algumas linhas de código, você viu como pode registrar métricas em log para o Azure Machine Learning.

Há outras maneiras de criar ambientes do Azure Machine Learning, incluindo [por meio de um arquivo requirements.txt do Pip](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-) ou [de um ambiente Conda local existente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

Na próxima sessão, você verá como trabalhar com os dados no Azure Machine Learning carregando o conjunto de dados CIFAR10 para o Azure.

> [!div class="nextstepaction"]
> [Tutorial: Traga seus próprios dados](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Caso deseje concluir a série de tutoriais aqui e não progredir para a próxima etapa, lembre-se de [limpar seus recursos](tutorial-1st-experiment-bring-data.md#clean-up-resources).
