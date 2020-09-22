---
title: 'Tutorial: Executar o script "Olá, Mundo" do Python'
titleSuffix: Azure Machine Learning
description: A parte 2 da série de introdução do Azure ML mostra como enviar um script "Olá, mundo" do Python trivial para a nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 98f4c30d03763e070b1bdc32a5e6e099556916ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929181"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Tutorial: Executar o script "Olá, Mundo" do Python (Parte 2 de 4)

Neste tutorial, você aprenderá a usar o SDK do Python do Azure Machine Learning para enviar e executar um script "Olá, Mundo" do Python.

Este tutorial é a **parte dois de uma série de tutoriais de quatro partes** em que você aprende os conceitos básicos do Azure Machine Learning e tarefas completas de machine learning baseadas em trabalhos no Azure. Este tutorial se baseia no trabalho que você concluiu no [Tutorial parte 1: Configurar seu computador local para o Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar e executar um script "Olá, Mundo" do Python localmente
> * Criar um script de controle do Python para enviar "Olá, mundo" para o Azure Machine Learning
> * Entender os conceitos de Azure Machine Learning no script de controle
> * Enviar e executar "Olá, Mundo"
> * Exibir a saída do código na nuvem

## <a name="prerequisites"></a>Pré-requisitos

- Conclua a [Parte 1 do tutorial sobre a configuração do computador local](tutorial-1st-experiment-sdk-setup-local.md) se você ainda não tem um workspace do Azure Machine Learning.
- Conhecimento introdutório da linguagem Python e dos fluxos de trabalho de machine learning.
- Ambiente de desenvolvimento local. Isso inclui, entre outros, Visual Studio Code, Jupyter ou PyCharm.
- Python (versão 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Criar e executar um script do Python localmente

Crie um subdiretório chamado `src` no diretório `tutorial` para armazenar o código que você deseja executar em um cluster de computação do Azure Machine Learning. No subdiretório `src`, crie o script do Python `hello.py`:

```python
# src/hello.py
print("Hello world!")
```

A estrutura do diretório do projeto agora terá a seguinte aparência:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Testar seu script localmente

Você pode executar seu código localmente, o que tem o benefício da depuração interativa de código, usando seu IDE favorito ou por meio de um terminal:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Criar um script de controle

Um *script de controle* permite que você execute o script `hello.py` na nuvem.  O script de controle permite que você controle como e em que local seu código de machine learning é executado.  

No diretório do tutorial, crie um arquivo Python chamado `03-run-hello.py` e copie e cole o código abaixo nesse arquivo:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Compreender o código

Aqui está uma descrição de como o script de controle funciona:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      O [workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) conecta-se ao workspace do Azure Machine Learning, de modo que você possa se comunicar com seus recursos do Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      O [experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) oferece uma forma simples de organizar várias execuções com um único nome. Mais tarde, você pode ver como os experimentos facilitam a comparação de métricas entre dezenas de execuções.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) encapsula seu código `hello.py` e o passa para seu workspace. Como o nome sugere, você pode usar essa classe para _configurar_ como deseja que seu _script_ seja _executado_ no Azure Machine Learning. Também especifica em qual destino de computação o script será executado.  Nesse código, o destino é o cluster de computação que você criou no [tutorial de instalação](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Envia o seu script. Esse envio é chamado de [Execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true).  Uma execução encapsula uma única execução do seu código. Use uma execução para monitorar o progresso do script, capturar a saída, analisar os resultados, visualizar métricas e muito mais.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        O objeto `run` fornece um identificador na execução do seu código. Monitore seu progresso do Azure Machine Learning Studio com a URL impressa do script Python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Enviar e executar seu código na nuvem

Execute o script de controle que, por sua vez, executa `hello.py` no cluster de computação que você criou no [tutorial de instalação](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Monitorar seu código na nuvem usando o Studio

A saída conterá um link para o Azure Machine Learning Studio semelhante a este: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Siga o link e navegue até a guia **Saídas + logs**. Lá, você pode ver um arquivo `70_driver_log.txt` como este:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Na linha 8, você vê "Olá, mundo!" saída.

O arquivo `70_driver_log.txt` contém a saída padrão de uma execução. Esse arquivo pode ser útil ao depurar execuções remotas na nuvem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você pegou um script "Olá, mundo" simples e o executou no Azure. Você viu como se conectar ao seu workspace do Azure Machine Learning, criar um experimento e enviar seu código de `hello.py` para a nuvem.

No próximo tutorial, você vai aprimorar esses aprendizados executando algo mais interessante do que `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Se você quiser concluir a série de tutoriais aqui e não progredir para a próxima etapa, [limpe seus recursos](tutorial-1st-experiment-bring-data.md#clean-up-resources)
