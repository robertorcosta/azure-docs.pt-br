---
title: Depurar & solucionar problemas de pipelines de ML
titleSuffix: Azure Machine Learning
description: Depure seus pipelines de Azure Machine Learning no Python. Aprenda armadilhas comuns para desenvolver pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota. Saiba como usar Visual Studio Code para depurar interativamente seus pipelines de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 9c2e00ed14a45c6df7cf72845db2ecd069381ca5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257194"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e solucionar problemas de pipelines do aprendizado de máquina
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a depurar e solucionar problemas de [pipelines do Machine Learning](concept-ml-pipelines.md) no [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e no [Designer de Azure Machine Learning (versão prévia)](https://docs.microsoft.com/azure/machine-learning/concept-designer). As informações são fornecidas sobre como:

* Depurar usando o SDK do Azure Machine Learning
* Depurar usando o designer de Azure Machine Learning
* Depurar usando Application Insights
* Depurar interativamente usando Visual Studio Code (VS Code) e o Ferramentas Python para Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Depuração e solução de problemas no SDK do Azure Machine Learning
As seções a seguir fornecem uma visão geral das armadilhas comuns ao criar pipelines e estratégias diferentes para depurar seu código em execução em um pipeline. Use as dicas a seguir quando estiver tendo problemas para fazer com que um pipeline seja executado conforme o esperado.

### <a name="testing-scripts-locally"></a>Testando scripts localmente

Uma das falhas mais comuns em um pipeline é que um script anexado (script de limpeza de dados, script de pontuação, etc.) não está em execução como pretendido, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de Depurar em seu espaço de trabalho no Azure Machine Learning Studio. 

Os pipelines propriamente ditos não podem ser executados localmente, mas a execução dos scripts em isolamento no computador local permite que você depure mais rápido porque não precisa esperar o processo de compilação do ambiente e da computação. Alguns trabalhos de desenvolvimento são necessários para fazer isso:

* Se os seus dados estiverem em um armazenamento em nuvem, será necessário baixar os dados e disponibilizá-los para o script. Usar uma pequena amostra de seus dados é uma boa maneira de reduzir o tempo de execução e obter rapidamente comentários sobre o comportamento do script
* Se você estiver tentando simular uma etapa intermediária de pipeline, talvez seja necessário criar manualmente os tipos de objeto que o script específico está esperando na etapa anterior
* Você também precisará definir seu próprio ambiente e replicar as dependências definidas em seu ambiente de computação remota

Quando você tiver uma configuração de script para ser executada no seu ambiente local, será muito mais fácil fazer tarefas de depuração como:

* Anexando uma configuração de depuração personalizada
* Pausando a execução e inspecionando o estado do objeto
* Captura de tipo ou erros lógicos que não serão expostos até o tempo de execução

> [!TIP] 
> Depois que você puder verificar se o script está sendo executado conforme o esperado, uma boa próxima etapa é executar o script em um pipeline de etapa única antes de tentar executá-lo em um pipeline com várias etapas.

### <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

O teste de scripts localmente é uma ótima maneira de depurar fragmentos de código principais e lógica complexa antes de começar a criar um pipeline, mas, em algum momento, você provavelmente precisará depurar scripts durante a execução real do pipeline, especialmente ao diagnosticar o comportamento que ocorre durante a interação entre as etapas do pipeline. É recomendável liberal o `print()` uso de instruções em seus scripts de etapa para que você possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante a como você depuraria o código JavaScript.

O arquivo `70_driver_log.txt` de log contém: 

* Todas as instruções impressas durante a execução do script
* O rastreamento de pilha para o script 

Para localizar esse e outros arquivos de log no portal, primeiro clique no pipeline executado no seu espaço de trabalho.

![Página de lista de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navegue até a página de detalhes da execução do pipeline.

![Página de detalhes da execução do pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Clique no módulo para a etapa específica. Navegue até a guia **logs** . Outros logs incluem informações sobre o processo de compilação da imagem do ambiente e os scripts de preparação de etapa.

![Guia log da página detalhes da execução do pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Execuções para *pipelines publicados* podem ser encontradas na guia **pontos de extremidade** em seu espaço de trabalho. Execuções para *pipelines não publicados* podem ser encontradas em **experimentos** ou **pipelines**.

### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

A tabela a seguir contém problemas comuns durante o desenvolvimento de pipeline, com possíveis soluções.

| Problema | Solução possível |
|--|--|
| Não é possível passar dados `PipelineData` para o diretório | Verifique se você criou um diretório no script que corresponde a onde o seu pipeline espera os dados de saída da etapa. Na maioria dos casos, um argumento de entrada definirá o diretório de saída e, em seguida, você criará o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Consulte o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para obter um exemplo de script de pontuação que mostra esse padrão de design. |
| Bugs de dependência | Se você tiver desenvolvido e testado com scripts localmente, mas encontrar problemas de dependência ao executar em uma computação remota no pipeline, verifique se as dependências e as versões do ambiente de computação correspondem ao ambiente de teste. (Consulte [criação de ambiente, Caching e reutilização](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Erros ambíguos com destinos de computação | Excluir e recriar destinos de computação pode resolver determinados problemas com destinos de computação. |
| O pipeline não está Reutilizando as etapas | A reutilização de etapa é habilitada por padrão, mas certifique-se de que você não a desabilitou em uma etapa de pipeline. Se a reutilização estiver desabilitada, o `allow_reuse` parâmetro na etapa será definido `False`como. |
| O pipeline está sendo executado desnecessariamente | Para garantir que as etapas sejam executadas apenas novamente quando os dados ou scripts subjacentes forem alterados, desassocie os diretórios para cada etapa. Se você usar o mesmo diretório de origem para várias etapas, poderá ocorrer uma reexecutação desnecessária. Use o `source_directory` parâmetro em um objeto Step de pipeline para apontar para seu diretório isolado para essa etapa e verifique se você não está usando `source_directory` o mesmo caminho para várias etapas. |

### <a name="logging-options-and-behavior"></a>Opções e comportamento de log

A tabela a seguir fornece informações para opções de depuração diferentes para pipelines. Não é uma lista completa, já que existem outras opções, além das Azure Machine Learning, Python e OpenCensus mostradas aqui.

| Biblioteca                    | Type   | Exemplo                                                          | Destino                                  | Recursos                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK do Azure Machine Learning | Métrica | `run.log(name, val)`                                             | Interface do usuário do portal do Azure Machine Learning             | [Como acompanhar experimentos](how-to-track-experiments.md#available-metrics-to-track)<br>[classe azureml. Core. Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Impressão/log do Python    | Log    | `print(val)`<br>`logging.info(message)`                          | Logs de driver, designer de Azure Machine Learning | [Como acompanhar experimentos](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Registro em log do Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Rastreamentos de Application Insights                | [Depurar pipelines no Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Exportadores OpenCensus Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Guia de registro em log do Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exemplo de opções de log

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Depuração e solução de problemas no designer de Azure Machine Learning (versão prévia)

Esta seção fornece uma visão geral de como solucionar problemas de pipelines no designer.
Para pipelines criados no designer, você pode encontrar os **arquivos de log** na página de criação ou na página de detalhes de execução do pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Acessar logs da página de criação

Ao enviar uma execução de pipeline e permanecer na página de criação, você pode encontrar os arquivos de log gerados para cada módulo.

1. Selecione qualquer módulo na tela de criação.
1. No painel direito do módulo, vá para a guia **saídas + logs** .
1. Selecione o arquivo `70_driver_log.txt`de log.

    ![Criando logs de módulo de página](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Acessar logs de execuções de pipeline

Você também pode encontrar os arquivos de log de execuções específicas na página de detalhes de execução do pipeline nas seções **pipelines** ou **experimentos** .

1. Selecione uma execução de pipeline criada no designer.
    ![Página de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Selecione qualquer módulo no painel de visualização.
1. No painel direito do módulo, vá para a guia **saídas + logs** .
1. Selecione o arquivo `70_driver_log.txt`de log.

## <a name="debug-and-troubleshoot-in-application-insights"></a>Depuração e solução de problemas no Application Insights
Para obter mais informações sobre como usar a biblioteca do OpenCensus Python dessa maneira, consulte este guia: [depurar e solucionar problemas de pipelines do Machine Learning no Application insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Depuração e solução de problemas no Visual Studio Code

Em alguns casos, talvez seja necessário depurar interativamente o código Python usado em seu pipeline de ML. Usando Visual Studio Code (VS Code) e o Ferramentas Python para Visual Studio (PTVSD), você pode anexar ao código conforme ele é executado no ambiente de treinamento.

### <a name="prerequisites"></a>Pré-requisitos

* Um __espaço de trabalho Azure Machine Learning__ configurado para usar uma __rede virtual do Azure__.
* Um __pipeline Azure Machine Learning__ que usa scripts Python como parte das etapas do pipeline. Por exemplo, um PythonScriptStep.
* Um cluster de computação Azure Machine Learning, que está __na rede virtual__ e é __usado pelo pipeline para treinamento__.
* Um __ambiente de desenvolvimento__ que está __na rede virtual__. O ambiente de desenvolvimento pode ser um dos seguintes:

    * Uma máquina virtual do Azure na rede virtual
    * Uma instância de computação da VM do notebook na rede virtual
    * Um computador cliente conectado à rede virtual por uma VPN (rede virtual privada).

Para saber mais sobre como usar uma rede virtual do Azure com Azure Machine Learning, confira [trabalhos de inferência e experimentação seguros do Azure ml em uma rede virtual do Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Como isso funciona

Suas etapas de pipeline ML executam scripts Python. Esses scripts são modificados para executar as seguintes ações:
    
1. Registre o endereço IP do host no qual eles estão sendo executados. Você usa o endereço IP para conectar o depurador ao script.

2. Inicie o componente de depuração PTVSD e aguarde até que um depurador se conecte.

3. Em seu ambiente de desenvolvimento, você monitora os logs criados pelo processo de treinamento para localizar o endereço IP em que o script está sendo executado.

4. Você informa VS Code o endereço IP para conectar o depurador usando um `launch.json` arquivo.

5. Anexe o depurador e percorra o script interativamente.

### <a name="configure-python-scripts"></a>Configurar scripts do Python

Para habilitar a depuração, faça as seguintes alterações no (s) script (es) do Python usados por etapas em seu pipeline de ML:

1. Adicione as seguintes instruções de importação:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Adicione os argumentos a seguir. Esses argumentos permitem que você habilite o depurador conforme necessário e defina o tempo limite para anexar o depurador:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Adicione as instruções a seguir. Essas instruções carregam o contexto de execução atual para que você possa registrar em log o endereço IP do nó em que o código está sendo executado:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adicione uma `if` instrução que inicia PTVSD e aguarda a anexação de um depurador. Se nenhum depurador for anexado antes do tempo limite, o script continuará normalmente.

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

O exemplo de Python a seguir mostra `train.py` um arquivo básico que habilita a depuração:

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

### <a name="configure-ml-pipeline"></a>Configurar pipeline ML

Para fornecer os pacotes do python necessários para iniciar o PTVSD e obter o contexto de execução, crie um `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`ambiente e defina. Altere a versão do SDK para corresponder à que você está usando. O trecho de código a seguir demonstra como criar um ambiente:

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

Na seção [Configurar scripts Python](#configure-python-scripts) , dois novos argumentos foram adicionados aos scripts usados pelas etapas de pipeline do ml. O trecho de código a seguir demonstra como usar esses argumentos para habilitar a depuração para o componente e definir um tempo limite. Ele também demonstra como usar o ambiente criado anteriormente por meio da `runconfig=run_config`configuração:

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

Quando o pipeline é executado, cada etapa cria uma execução filho. Se a depuração estiver habilitada, o script modificado registrará informações semelhantes ao seguinte texto `70_driver_log.txt` no para a execução do filho:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Salve o `ip_address` valor. Ele é usado na próxima seção.

> [!TIP]
> Você também pode encontrar o endereço IP na etapa executar logs para a execução do filho para este pipeline. Para obter mais informações sobre como exibir essas informações, consulte [monitorar execuções e métricas de experimento do Azure ml](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o Ferramentas Python para Visual Studio (PTVSD) em seu ambiente de desenvolvimento de VS Code, use o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como usar o PTVSD com VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar VS Code para se comunicar com a computação Azure Machine Learning que está executando o depurador, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __depurar__ e, em seguida, selecione __configurações abertas__. Um arquivo chamado __Launch. JSON__ é aberto.

    1. No arquivo __Launch. JSON__ , localize a linha que contém `"configurations": [`e insira o texto a seguir depois dela. Altere a `"host": "10.3.0.5"` entrada para o endereço IP retornado nos logs da seção anterior. Altere a `"localRoot": "${workspaceFolder}/code/step"` entrada para um diretório local que contenha uma cópia do script que está sendo depurado:

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
        > Se já houver outras entradas na seção Configurações, adicione uma vírgula (,) após o código que você inseriu.

        > [!TIP]
        > A prática recomendada é manter os recursos de scripts em diretórios separados, motivo pelo qual o valor `localRoot` de exemplo faz `/code/step1`referência.
        >
        > Se você estiver Depurando vários scripts, em diretórios diferentes, crie uma seção de configuração separada para cada script.

    1. Salve o arquivo __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Conectar o depurador

1. Abra VS Code e abra uma cópia local do script.
2. Defina os pontos de interrupção onde você deseja que o script Pare depois de anexado.
3. Enquanto o processo filho estiver executando o script e o `Timeout for debug connection` for exibido nos logs, use a tecla F5 ou selecione __depurar__. Quando solicitado, selecione o __Azure Machine Learning computação: configuração de depuração remota__ . Você também pode selecionar o ícone de depuração na barra lateral, a __Azure Machine Learning: entrada de depuração remota__ no menu suspenso de depuração e, em seguida, usar a seta verde para anexar o depurador.

    Neste ponto, VS Code se conecta ao PTVSD no nó de computação e pára no ponto de interrupção definido anteriormente. Agora você pode percorrer o código conforme ele é executado, exibir variáveis, etc.

    > [!NOTE]
    > Se o log exibir uma entrada informando `Debugger attached = False`, o tempo limite expirou e o script continuou sem o depurador. Envie o pipeline novamente e conecte o depurador após a `Timeout for debug connection` mensagem e antes do tempo limite expirar.

## <a name="next-steps"></a>Próximas etapas

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Consulte a lista de [exceções de designer e códigos de erro](algorithm-module-reference/designer-error-codes.md).
