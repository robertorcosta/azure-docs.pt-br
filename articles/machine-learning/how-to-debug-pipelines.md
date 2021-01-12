---
title: Solução de problemas de pipelines de ML
titleSuffix: Azure Machine Learning
description: Como solucionar problemas ao obter erros ao executar um pipeline do Machine Learning. Armadilhas e dicas comuns para ajudar a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: d55a9ff4dc2a639fca67d19d9323b9397aa0f409
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070364"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Solucionando problemas de pipelines do Machine Learning

Neste artigo, você aprenderá a solucionar problemas ao obter erros ao executar um [pipeline do Machine Learning](concept-ml-pipelines.md) no [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) e no designer de [Azure Machine Learning](./concept-designer.md). 

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

A tabela a seguir contém problemas comuns durante o desenvolvimento de pipeline, com possíveis soluções.

| Problema | Solução possível |
|--|--|
| Não é possível passar dados para o `PipelineData` diretório | Verifique se você criou um diretório no script que corresponde a onde o seu pipeline espera os dados de saída da etapa. Na maioria dos casos, um argumento de entrada definirá o diretório de saída e, em seguida, você criará o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Consulte o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para obter um exemplo de script de pontuação que mostra esse padrão de design. |
| Bugs de dependência | Se você vir erros de dependência em seu pipeline remoto que não ocorreram durante o teste local, confirme se as suas dependências e versões de ambiente remoto correspondem àquelas em seu ambiente de teste. (Consulte [criação de ambiente, Caching e reutilização](./concept-environments.md#environment-building-caching-and-reuse)|
| Erros ambíguos com destinos de computação | Tente excluir e recriar destinos de computação. Recriar destinos de computação é rápido e pode resolver alguns problemas transitórios. |
| O pipeline não está Reutilizando as etapas | A reutilização de etapa é habilitada por padrão, mas certifique-se de que você não a desabilitou em uma etapa de pipeline. Se a reutilização estiver desabilitada, o `allow_reuse` parâmetro na etapa será definido como `False` . |
| O pipeline está sendo executado desnecessariamente | Para garantir que as etapas sejam executadas somente quando seus dados ou scripts subjacentes forem alterados, desassocie os diretórios de código-fonte para cada etapa. Se você usar o mesmo diretório de origem para várias etapas, poderá ocorrer uma reexecutação desnecessária. Use o `source_directory` parâmetro em um objeto Step de pipeline para apontar para seu diretório isolado para essa etapa e verifique se você não está usando o mesmo `source_directory` caminho para várias etapas. |
| Etapa reduzindo as épocas de treinamento ou outro comportamento de looping | Tente alternar qualquer gravação de arquivo, incluindo registro em log, de `as_mount()` para `as_upload()` . O modo de **montagem** usa um sistema de arquivos virtualizado remoto e carrega todo o arquivo cada vez que é anexado. |
| O destino de computação leva muito tempo para iniciar | As imagens do Docker para destinos de computação são carregadas do ACR (registro de contêiner do Azure). Por padrão, Azure Machine Learning cria um ACR que usa a camada de serviço *básica* . Alterar o ACR para o seu espaço de trabalho para a camada Standard ou Premium pode reduzir o tempo necessário para criar e carregar imagens. Para obter mais informações, confira [Níveis de serviço do Registro de Contêiner do Azure](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.

## <a name="debugging-techniques"></a>Técnicas de depuração

Há três técnicas principais para a depuração de pipelines: 

* Depurar etapas de pipeline individuais no computador local
* Use registro em log e Application Insights para isolar e diagnosticar a origem do problema
* Anexar um depurador remoto a um pipeline em execução no Azure

### <a name="debug-scripts-locally"></a>Depurar scripts localmente

Uma das falhas mais comuns em um pipeline é que o script de domínio não é executado conforme o esperado, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de depurar.

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

## <a name="configure-write-to-and-review-pipeline-logs"></a>Configurar, gravar e examinar logs de pipeline

O teste de scripts localmente é uma ótima maneira de depurar fragmentos de código principais e lógica complexa antes de começar a criar um pipeline, mas, em algum momento, você provavelmente precisará depurar scripts durante a execução real do pipeline, especialmente ao diagnosticar o comportamento que ocorre durante a interação entre as etapas do pipeline. É recomendável liberal o uso de `print()` instruções em seus scripts de etapa para que você possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante a como você depuraria o código JavaScript.

### <a name="logging-options-and-behavior"></a>Opções e comportamento de log

A tabela a seguir fornece informações para opções de depuração diferentes para pipelines. Não é uma lista completa, já que existem outras opções, além das Azure Machine Learning, Python e OpenCensus mostradas aqui.

| Biblioteca                    | Type   | Exemplo                                                          | Destino                                  | Recursos                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK do Azure Machine Learning | Metric | `run.log(name, val)`                                             | Interface do usuário do portal do Azure Machine Learning             | [Como acompanhar experimentos](how-to-track-experiments.md)<br>[classe azureml. Core. Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)                                                                                                                                                 |
| Impressão/log do Python    | Registro    | `print(val)`<br>`logging.info(message)`                          | Logs de driver, designer de Azure Machine Learning | [Como acompanhar experimentos](how-to-track-experiments.md)<br><br>[Registro em log do Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Registro    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Rastreamentos de Application Insights                | [Depurar pipelines no Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[Exportadores OpenCensus Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Guia de registro em log do Python](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="azure-machine-learning-designer"></a>Designer do Azure Machine Learning

Para pipelines criados no designer, você pode encontrar o arquivo de **70_driver_log** na página de criação ou na página de detalhes de execução de pipeline.

### <a name="enable-logging-for-real-time-endpoints"></a>Habilitar o registro em log para pontos de extremidade em tempo real

Para solucionar problemas e depurar pontos de extremidade em tempo real no designer, você deve habilitar o log do Application insights usando o SDK. O registro em log permite solucionar problemas e depurar questões de uso e implantação de modelo. Para obter mais informações, consulte [Logging for deployed Models](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Obter logs da página de criação

Ao enviar uma execução de pipeline e permanecer na página de criação, você poderá encontrar os arquivos de log gerados para cada módulo à medida que cada módulo terminar a execução.

1. Selecione um módulo que concluiu a execução na tela de criação.
1. No painel direito do módulo, vá para a guia  **saídas + logs** .
1. Expanda o painel direito e selecione o **70_driver_log.txt** para exibir o arquivo no navegador. Você também pode baixar logs localmente.

    ![Painel de saída expandido no designer](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Obter logs de execuções de pipeline

Você também pode encontrar os arquivos de log para execuções específicas na página de detalhes de execução do pipeline, que pode ser encontrada na seção **pipelines** ou **experimentos** do estúdio.

1. Selecione uma execução de pipeline criada no designer.

    ![Página de execução de pipeline](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecione um módulo no painel de visualização.
1. No painel direito do módulo, vá para a guia  **saídas + logs** .
1. Expanda o painel direito para exibir o arquivo de **70_driver_log.txt** no navegador ou selecione o arquivo para baixar os logs localmente.

> [!IMPORTANT]
> Para atualizar um pipeline da página de detalhes de execução do pipeline, você deve **clonar** a execução do pipeline para um novo rascunho do pipeline. Uma execução de pipeline é um instantâneo do pipeline. Ele é semelhante a um arquivo de log e não pode ser alterado. 

## <a name="application-insights"></a>Application Insights
Para obter mais informações sobre como usar a biblioteca do OpenCensus Python dessa maneira, consulte este guia: [depurar e solucionar problemas de pipelines do Machine Learning no Application insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Depuração interativa com Visual Studio Code

Em alguns casos, talvez seja necessário depurar interativamente o código Python usado em seu pipeline de ML. Usando Visual Studio Code (VS Code) e debugpy, você pode anexar ao código conforme ele é executado no ambiente de treinamento. Para obter mais informações, visite a [depuração interativa no guia vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Próximas etapas

* [Como depurar e solucionar problemas de ParallelRunStep](how-to-debug-parallel-run-step.md)

* Para obter um tutorial completo usando o `ParallelRunStep` , consulte [tutorial: criar um pipeline de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md).

* Para obter um exemplo completo mostrando o Machine Learning automatizado em pipelines de ML, consulte [usar o ml automatizado em um pipeline de Azure Machine Learning no Python](how-to-use-automlstep-in-pipelines.md).

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-pipelines-Core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) e o pacote [azureml-pipelines-Steps](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) .

* Consulte a lista de [exceções de designer e códigos de erro](algorithm-module-reference/designer-error-codes.md).