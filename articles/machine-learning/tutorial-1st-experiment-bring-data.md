---
title: 'Tutorial: Usar seus próprios dados'
titleSuffix: Azure Machine Learning
description: A Parte 4 da série de introdução do Azure ML mostra como usar seus próprios dados em uma execução de treinamento remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 876ba76655572979a1d831a1ca07e5f3871a3283
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929183"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Tutorial: Use seus próprios dados (Parte 4 de 4)

Este tutorial mostra como carregar e usar seus próprios dados para treinar modelos de machine learning no Azure Machine Learning.

Este tutorial é a **parte quatro de uma série de tutoriais de quatro partes** em que você aprende os conceitos básicos do Azure Machine Learning e tarefas completas de machine learning baseadas em trabalhos no Azure. Este tutorial se baseia no trabalho que você concluiu na [Parte 1: Configurar](tutorial-1st-experiment-sdk-setup-local.md), [Parte 2: Execute "Olá, Mundo"](tutorial-1st-experiment-hello-world.md) e [Parte 3: Treine um modelo](tutorial-1st-experiment-sdk-train.md).

Na [Parte 3: Treinar um modelo](tutorial-1st-experiment-sdk-train.md), os dados foram baixados usando o método `torchvision.datasets.CIFAR10` embutido na API do PyTorch. No entanto, em muitos casos, você vai querer usar os próprios dados em uma execução de treinamento remoto. Este artigo mostra o fluxo de trabalho que você pode usar para trabalhar com seus próprios dados no Azure Machine Learning.

Neste tutorial, você:

> [!div class="checklist"]
> * Configura o script de treinamento para usar dados em um diretório local
> * Testa o script de treinamento localmente
> * Carrega dados no Azure
> * Cria script de controle
> * Entende os novos conceitos de Azure Machine Learning (passando parâmetros, conjuntos de valores, armazenamentos de dados)
> * Envia e executa o script de treinamento
> * Exibe a saída do código na nuvem

## <a name="prerequisites"></a>Pré-requisitos

* Conclua a [Parte 3](tutorial-1st-experiment-sdk-train.md) da série.
* Conhecimento introdutório da linguagem Python e dos fluxos de trabalho de machine learning.
* Ambiente de desenvolvimento local. Isso inclui, entre outros, Visual Studio Code, Jupyter ou PyCharm.
* Python (versão 3.5-3.7).

## <a name="adjust-the-training-script"></a>Ajustar o script de treinamento
Agora você tem seu script de treinamento (tutorial/src/train.py) em execução no Azure Machine Learning e pode monitorar o desempenho do modelo. Vamos definir os parâmetros do script de treinamento introduzindo argumentos. O uso de argumentos permitirá que você compare facilmente diferentes hiperparâmetros.

No momento, nosso script de treinamento está definido para baixar o conjunto de dados CIFAR10 em cada execução. O código Python abaixo foi ajustado para ler os dados de um diretório.

>[!NOTE] 
> O uso de `argparse` para definir os parâmetros do script.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

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
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>Compreender as alterações de código

O código usado em `train.py` utilizou a biblioteca `argparse` para configurar `data_path`, `learning_rate` e `momentum`.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Além disso, o script `train.py` foi adaptado para atualizar o otimizador para usar os parâmetros definidos pelo usuário:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>Testar o script localmente

Seu script agora aceita _caminho de dados_ como um argumento. Para começar, teste-o localmente. Adicione à estrutura do diretório do tutorial uma pasta chamada `data`. A estrutura do diretório deve ser semelhante a:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Se você não executou `train.py` localmente no tutorial anterior, não tem o diretório `data/`. Nesse caso, execute o método `torchvision.datasets.CIFAR10` localmente com `download=True` no script `train.py`.

Para executar o script de treinamento modificado localmente, chame:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Você evita precisar baixar o conjunto de dados CIFAR10 passando um caminho local para os dados. Além disso, você pode experimentar valores diferentes para os hiperparâmetros _taxa de aprendizado_ e _momento_ sem precisar embuti-los no script de treinamento.

## <a name="upload-the-data-to-azure"></a>Carregar os dados para o Azure

Para executar esse script no Azure Machine Learning, você precisa disponibilizar seus dados de treinamento no Azure. Seu workspace do Azure Machine Learning vem equipado com um _armazenamento de dados_ **padrão**, uma conta de armazenamento de blobs do Azure, que você pode usar para armazenar os dados de treinamento.

>[!NOTE] 
> O Azure Machine Learning permite que você se conecte a outros armazenamentos de dados baseados em nuvem que armazenam seus dados. Para obter mais detalhes, confira a [documentação de armazenamentos de dados](./concept-data.md).  

Crie um script de controle do Python chamado `05-upload-data.py` no diretório `tutorial`:

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

O `target_path` especifica o caminho no armazenamento de dados para o qual os dados do CIFAR10 serão carregados.

>[!TIP] 
> Embora você esteja usando Azure Machine Learning para carregar os dados, pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar arquivos ad hoc. Se você precisar de uma ferramenta ETL, o [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) poderá ser usado para ingerir seus dados para o Azure.

Execute o arquivo Python para carregar os dados (Observação: o upload deve ser rápido, menos de 60 segundos.)

```bash
python 05-upload-data.py
```
Você deverá ver a seguinte saída padrão:
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>Criar um script de controle

Como você fez anteriormente, crie um script de controle Python chamado `06-run-pytorch-data.py`:

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Entender as alterações de código

O script de controle é semelhante àquele da [Parte 3 desta série](tutorial-1st-experiment-sdk-train.md) com as seguintes novas linhas:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Um [conjunto de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) é usado para fazer referência a os dados que você carregou no Armazenamento de Blobs do Azure. Os conjuntos de dados são uma camada de abstração sobre seus dados projetada para melhorar a confiabilidade e a confiança.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) é modificado para incluir uma lista de argumentos que serão passados para `train.py`. O argumento `dataset.as_named_input('input').as_mount()` significa que o diretório especificado será _montado_ para o destino de computação.
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Execução de envio para o Azure Machine Learning

Agora, reenvie a execução para usar a nova configuração:

```bash
python 06-run-pytorch-data.py
```

Isso imprimirá uma URL para o experimento no Azure Machine Learning Studio. Se você navegar até esse link, poderá ver seu código em execução.

### <a name="inspect-the-70_driver_log-log-file"></a>Inspecione o arquivo de log 70_driver_log

No Azure Machine Learning Studio, navegue até a execução do experimento (clicando na saída da URL da célula acima) seguida de **Saídas + logs**. Clique no arquivo 70_driver_log. txt – você deverá ver a seguinte saída:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Aviso:

1. O Azure Machine Learning montou o armazenamento de blobs no cluster de computação automaticamente para você.
2. O ``dataset.as_named_input('input').as_mount()`` usado no script de controle é resolvido para o ponto de montagem

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, vimos como carregar dados no Azure usando um `Datastore`. O armazenamento de dados atuou como armazenamento em nuvem para seu workspace, fornecendo um local persistente e flexível para manter seus dados.

Você viu como modificar seu script de treinamento para aceitar um caminho de dados por meio da linha de comando. Usando um `Dataset`, você conseguiu montar um diretório para a execução remota. 

Agora que você tem um modelo, saiba:

* Como [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md)
