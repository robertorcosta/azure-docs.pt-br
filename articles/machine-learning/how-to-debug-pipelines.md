---
title: Depurar e solucionar problemas de pipelines do aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Depurar e solucionar problemas de aprendizado de máquina pipelines no Azure Machine Learning SDK para Python. Aprenda armadilhas comuns para o desenvolvimento de pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota. Aprenda a usar o Visual Studio Code para depurar interativamente seus pipelines de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064073"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e solucionar problemas de pipelines do aprendizado de máquina
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como depurar e solucionar [problemas de pipelines](concept-ml-pipelines.md) de aprendizagem de máquina no [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [no Azure Machine Learning designer (preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informações são fornecidas sobre como:

* Depuração usando o Azure Machine Learning SDK
* Depuração usando o Designer de Aprendizado de Máquina do Azure
* Depuração usando insights de aplicativos
* Depurar interativamente usando o Visual Studio Code (VS Code) e as Ferramentas Python para O Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Depuração e solução de problemas no SDK de aprendizado de máquina do Azure
As seções a seguir fornecem uma visão geral das armadilhas comuns ao construir dutos e diferentes estratégias para depurar seu código que está sendo executado em um pipeline. Use as seguintes dicas quando estiver tendo problemas para fazer com que um pipeline seja executado como esperado.

### <a name="testing-scripts-locally"></a>Testando scripts localmente

Uma das falhas mais comuns em um pipeline é que um script conectado (script de limpeza de dados, script de pontuação, etc.) não está sendo executado como planejado, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de depurar em seu espaço de trabalho na Máquina Azure Estúdio de aprendizagem. 

Os pipelines em si não podem ser executados localmente, mas executar os scripts isoladamente em sua máquina local permite que você depurar mais rápido porque você não precisa esperar pelo processo de compilação e construção de ambientes. Alguns trabalhos de desenvolvimento são necessários para fazer isso:

* Se seus dados estão em um armazenamento de dados na nuvem, você precisará baixar dados e disponibilizá-los para o seu script. Usar uma pequena amostra de seus dados é uma boa maneira de reduzir o tempo de execução e obter rapidamente feedback sobre o comportamento do script
* Se você estiver tentando simular uma etapa intermediária do pipeline, talvez seja necessário construir manualmente os tipos de objeto que o script em particular está esperando da etapa anterior
* Você também precisará definir seu próprio ambiente e replicar as dependências definidas em seu ambiente de computação remota

Uma vez que você tenha uma configuração de script para executar em seu ambiente local, é muito mais fácil fazer tarefas de depuração como:

* Anexando uma configuração de depuração personalizada
* Pausando a execução e inspecionando o estado-objeto
* Tipo de captura ou erros lógicos que não serão expostos até o tempo de execução

> [!TIP] 
> Uma vez que você possa verificar se seu script está funcionando como esperado, um bom próximo passo é executar o script em um pipeline de passo único antes de tentar executá-lo em um pipeline com várias etapas.

### <a name="debugging-scripts-from-remote-context"></a>Depuração de scripts de contexto remoto

Testar scripts localmente é uma ótima maneira de depurar fragmentos de código principais e lógica complexa antes de começar a construir um pipeline, mas em algum momento você provavelmente precisará depurar scripts durante a execução real do pipeline em si, especialmente quando diagnosticar o comportamento que ocorre durante a interação entre as etapas do pipeline. Recomendamos o `print()` uso liberal de instruções em seus scripts de passo para que você possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante à forma como você depuraria o código JavaScript.

O arquivo `70_driver_log.txt` de registro contém: 

* Todas as declarações impressas durante a execução do seu script
* O traço de pilha para o script 

Para encontrar este e outros arquivos de log no portal, primeiro clique no pipeline executado em seu espaço de trabalho.

![Página da lista de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navegue até a página de detalhes de execução do pipeline.

![Página de detalhes de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Clique no módulo para obter a etapa específica. Navegue até a guia **Logs.** Outros registros incluem informações sobre o processo de construção de imagens do ambiente e scripts de preparação de etapas.

![Guia de registro de página de detalhamento do pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> As corridas para *pipelines publicados* podem ser encontradas na guia **Endpoints** em seu espaço de trabalho. As corridas para *pipelines não publicados* podem ser encontradas em **Experimentos** ou **Pipelines**.

### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

A tabela a seguir contém problemas comuns durante o desenvolvimento de gasodutos, com possíveis soluções.

| Problema | Solução possível |
|--|--|
| Não é possível `PipelineData` passar dados para o diretório | Certifique-se de ter criado um diretório no script que corresponde a onde o pipeline espera os dados de saída de etapa. Na maioria dos casos, um argumento de entrada definirá o diretório de saída e, em seguida, você criará o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Veja o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) de um exemplo de script de pontuação que mostra esse padrão de design. |
| Bugs de dependência | Se você desenvolveu e testou scripts localmente, mas encontra problemas de dependência ao executar em uma computação remota no pipeline, certifique-se de que suas dependências e versões do ambiente computacional correspondam ao seu ambiente de teste. (Veja [construção, cache e reutilização do ambiente](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Erros ambíguos com metas de computação | Excluir e recriar metas de computação pode resolver certos problemas com metas de computação. |
| Pipeline não reutilizando etapas | A reutilização da etapa é ativada por padrão, mas certifique-se de que você não o desativou em uma etapa de pipeline. Se a reutilização `allow_reuse` for desativada, o `False`parâmetro na etapa será definido como . |
| Pipeline está sendo reexecutado desnecessariamente | Para garantir que as etapas só sejam reexecutadas quando seus dados ou scripts subjacentes mudarem, desacoplar seus diretórios para cada etapa. Se você usar o mesmo diretório de origem para várias etapas, poderá sofrer reprises desnecessárias. Use `source_directory` o parâmetro em um objeto de passo de pipeline para apontar para o diretório `source_directory` isolado para essa etapa e certifique-se de que você não está usando o mesmo caminho para várias etapas. |

### <a name="logging-options-and-behavior"></a>Opções de registro e comportamento

A tabela abaixo fornece informações para diferentes opções de depuração para pipelines. Não é uma lista exaustiva, pois existem outras opções além das de Aprendizado de Máquina Azure, Python e OpenCensus mostradas aqui.

| Biblioteca                    | Type   | Exemplo                                                          | Destino                                  | Recursos                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK do Azure Machine Learning | Métrica | `run.log(name, val)`                                             | UI do Portal de Aprendizagem de Máquina do Azure             | [Como acompanhar experimentos](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Impressão/registro de python    | Log    | `print(val)`<br>`logging.info(message)`                          | Registros de driver, Azure Machine Learning Designer | [Como acompanhar experimentos](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Registro de python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Insights de aplicativos - traces                | [Depurar pipelines no Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Exportadores OpenCensus Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Livro de receitas de registro python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exemplo de opções de registro

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Depuração e solução de problemas no designer de Machine Learning do Azure (visualização)

Esta seção fornece uma visão geral de como solucionar problemas de pipelines no designer.
Para pipelines criados no designer, você pode encontrar os **arquivos de log** na página de criação ou na página de detalhes de execução do pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Acesse os registros da página de autoria

Quando você envia uma execução de pipeline e permanece na página de autoria, você pode encontrar os arquivos de log gerados para cada módulo.

1. Selecione qualquer módulo na tela de autoria.
1. No painel direito do módulo, vá para a guia **Saídas+ogs.**
1. Selecione o arquivo de log`70_driver_log.txt`

    ![Registros do módulo da página de autoria](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Logs de acesso de tubulações de gasodutos

Você também pode encontrar os arquivos de log de executações específicas na página de detalhes de execução de pipeline nas seções **Pipelines** ou **Experimentos.**

1. Selecione uma execução de pipeline criada no designer.
    ![Página de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Selecione qualquer módulo no painel de visualização.
1. No painel direito do módulo, vá para a guia **Saídas+ogs.**
1. Selecione o arquivo de log`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Depuração e solução de problemas em Insights de Aplicativos
Para obter mais informações sobre como usar a biblioteca OpenCensus Python desta maneira, consulte este guia: [Depurar e solucionar problemas de pipelines de aprendizado de máquina no Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Depuração e solução de problemas no Visual Studio Code

Em alguns casos, você pode precisar depurar interativamente o código Python usado em seu pipeline ML. Usando o Visual Studio Code (VS Code) e o Python Tools for Visual Studio (PTVSD), você pode anexar ao código enquanto ele é executado no ambiente de treinamento.

### <a name="prerequisites"></a>Pré-requisitos

* Um __espaço de trabalho de aprendizado de máquina do Azure__ configurado para usar uma rede virtual do __Azure__.
* Um __pipeline de aprendizado de máquina do Azure__ que usa scripts Python como parte das etapas do pipeline. Por exemplo, um PythonScriptStep.
* Um cluster Azure Machine Learning Compute, que está __na rede virtual__ e é usado pelo pipeline para __treinamento.__
* Um __ambiente de desenvolvimento__ que está na rede __virtual.__ O ambiente de desenvolvimento pode ser um dos seguintes:

    * Uma Máquina Virtual Azure na rede virtual
    * Uma instância computacional do Notebook VM na rede virtual
    * Uma máquina cliente conectada à rede virtual por uma rede virtual privada (VPN).

Para obter mais informações sobre como usar uma rede virtual do Azure com o Azure Machine Learning, consulte [trabalhos de experimentação e inferência do Secure Azure ML dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Como ele funciona

Os passos do pipeline ml executam scripts Python. Esses scripts são modificados para executar as seguintes ações:
    
1. Registre o endereço IP do host em que eles estão sendo executados. Você usa o endereço IP para conectar o depurador ao script.

2. Inicie o componente de depuração PTVSD e aguarde a conexão de um depurador.

3. A partir do seu ambiente de desenvolvimento, você monitora os logs criados pelo processo de treinamento para encontrar o endereço IP onde o script está sendo executado.

4. Você diz ao VS Code o endereço IP para `launch.json` conectar o depurador usando um arquivo.

5. Você anexa o depurador e passo interativamente através do script.

### <a name="configure-python-scripts"></a>Configurar scripts Python

Para habilitar a depuração, faça as seguintes alterações nos scripts Python usados pelas etapas do pipeline ML:

1. Adicione as seguintes instruções de importação:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Adicione os seguintes argumentos. Esses argumentos permitem que você habilite o depurador conforme necessário e defina o tempo para anexar o depurador:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Adicione as seguintes instruções. Essas instruções carregam o contexto de execução atual para que você possa registrar o endereço IP do nó em que o código está sendo executado:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adicione `if` uma declaração que inicia o PTVSD e aguarda a anexação de um depurador. Se nenhum depurador for anexado antes do intervalo, o script continuará normalmente.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

O exemplo python a `train.py` seguir mostra um arquivo básico que permite a depuração:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurar o gasoduto ML

Para fornecer os pacotes Python necessários para iniciar o PTVSD `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`e obter o contexto de execução, crie um ambiente e um conjunto . Altere a versão SDK para corresponder à que você está usando. O seguinte trecho de código demonstra como criar um ambiente:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

Na seção [Configurar scripts Python,](#configure-python-scripts) dois novos argumentos foram adicionados aos scripts usados pelas etapas do pipeline ML. O trecho de código a seguir demonstra como usar esses argumentos para permitir a depuração do componente e definir um tempo hábil. Ele também demonstra como usar o ambiente `runconfig=run_config`criado anteriormente pela configuração :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando o gasoduto é executado, cada passo cria uma corrida infantil. Se a `70_driver_log.txt` depuração estiver ativada, o script modificado registra informações semelhantes ao seguinte texto no período de execução da criança:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Guarde `ip_address` o valor. Ele é usado na próxima seção.

> [!TIP]
> Você também pode encontrar o endereço IP dos logs de execução para a execução de crianças para esta etapa do pipeline. Para obter mais informações sobre a visualização dessas informações, consulte [as corridas e métricas do experimento Monitor Azure ML](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o Python Tools for Visual Studio (PTVSD) no seu ambiente de desenvolvimento de código VS, use o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como usar PTVSD com código VS, consulte [Depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o CÓDIGO VS para se comunicar com a computação azure Machine Learning que está executando o depurador, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __Depuração__ e selecione __Configurações abertas__. Um arquivo chamado __launch.json__ é aberto.

    1. No arquivo __launch.json,__ encontre a `"configurations": [`linha que contém e insira o texto a seguir após ele. Alterar `"host": "10.3.0.5"` a entrada para o endereço IP retornado em seus registros da seção anterior. Alterar `"localRoot": "${workspaceFolder}/code/step"` a entrada para um diretório local que contenha uma cópia do script que está sendo depurado:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na seção de configurações, adicione uma comuma (,) após o código inserido.

        > [!TIP]
        > A melhor prática é manter os recursos para scripts em `localRoot` diretórios `/code/step1`separados, razão pela qual o exemplo de referências de valor .
        >
        > Se você estiver depurando vários scripts, em diretórios diferentes, crie uma seção de configuração separada para cada script.

    1. Salve o arquivo __launch.json.__

### <a name="connect-the-debugger"></a>Conecte o depurador

1. Abra o CÓDIGO VS e abra uma cópia local do script.
2. Defina pontos de interrupção onde você deseja que o script pare depois de anexado.
3. Enquanto o processo filho estiver `Timeout for debug connection` executando o script e o é exibido nos registros, use a tecla F5 ou selecione __Debug__. Quando solicitado, selecione a __configuração Azure Machine Learning Compute: depuração remota.__ Você também pode selecionar o ícone de depuração na barra lateral, o __Azure Machine Learning: entrada remota de depuração__ no menu suspenso Debug e, em seguida, usar o arqueiro verde para anexar o depurador.

    Neste ponto, o VS Code conecta-se ao PTVSD no nó de computação e pára no ponto de ruptura que você define anteriormente. Agora você pode passar pelo código enquanto ele é executado, ver variáveis, etc.

    > [!NOTE]
    > Se o registro exibir `Debugger attached = False`uma entrada indicando, então o tempo hádia expirou e o script continuou sem o depurador. Envie o pipeline novamente e conecte `Timeout for debug connection` o depurador após a mensagem, e antes que o tempo expirar.

## <a name="next-steps"></a>Próximas etapas

* Consulte a referência sdk para obter ajuda com o pacote [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Veja a lista de exceções de [designers e códigos de erro](algorithm-module-reference/designer-error-codes.md).
