---
title: Como depurar e solucionar problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas do ParallelRunStep em pipelines do Machine Learning no SDK do Azure Machine Learning para Python. Aprenda armadilhas comuns para o desenvolvimento com pipelines e dicas para ajudá-lo a depurar seus scripts, antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: c4e2777f59bab8d7d874019004bff2e30395ab1d
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723469"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Como depurar e solucionar problemas de ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a depurar e solucionar problemas da classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a seção [Testar scripts localmente](how-to-debug-pipelines.md#testing-scripts-locally) para pipelines do Machine Learning. Seu ParallelRunStep é executado como uma etapa em pipelines de ML para que a mesma resposta se aplique a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurar scripts do contexto remoto

A transição da depuração de um script de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um passo difícil. Para obter informações sobre como localizar seus logs no portal, a seção [Pipelines do Machine Learning em scripts de depuração de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações contidas nessa seção também se aplicam a um ParallelRunStep.

Por exemplo, o arquivo de log `70_driver_log.txt` contém informações do controlador que inicia o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos do ParallelRunStep, há logs de várias fontes diferentes. No entanto, são criados dois arquivos consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: Esse arquivo fornece informações de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até o momento e o número de mini-lotes processados até o momento. Nesse final, ele mostra o resultado do trabalho. Se o trabalho tiver falhado, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/sys/master.txt`: Esse arquivo fornece a exibição do nó mestre (também conhecido como orquestrador) do trabalho em execução. Inclui a criação de tarefas, o monitoramento de progresso, o resultado da execução.

Os logs gerados do script de entrada usando o auxiliar EntryScript e as instruções PRINT serão encontrados nos seguintes arquivos:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Esses são os logs gravados de entry_script usando o auxiliar EntryScript. Também contém a instrução print (stdout) da entry_script.

Para uma compreensão concisa dos erros em seu script, há:

- `~/logs/user/error.txt`: Este arquivo tentará resumir os erros em seu script.

Para obter mais informações sobre erros no seu script, há:

- `~/logs/user/error/`: Contém todos os erros lançados e rastreamentos de pilha completos organizados por nó.

Quando você precisar de um entendimento completo de como cada nó executou o script de pontuação, examine os logs de processo individuais para cada nó. Os logs de processo podem ser encontrados na pasta `sys/node`, agrupados por nós de trabalho:

- `~/logs/sys/node/<node_name>.txt`: Esse arquivo fornece informações detalhadas sobre cada mini-lote à medida que ele é selecionado ou concluído por um trabalho. Para cada mini-lote, esse arquivo inclui:

    - O endereço IP e o PID do processo de trabalho. 
    - O número total de itens, contagem de itens processados com êxito e contagem de itens com falha.
    - A hora de início, a duração, o tempo de processamento e o tempo do método de execução.

Você também pode encontrar informações sobre o uso de recursos dos processos para cada trabalho. Essas informações estão no formato CSV e estão localizadas em `~/logs/sys/perf/overview.csv`. Para obter informações sobre cada processo, ele está disponível em `~logs/sys/processes.csv`.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como fazer log do meu script de usuário a partir de um contexto remoto?
Você pode obter um agente de EntryScript conforme mostrado no código de exemplo abaixo para fazer os logs aparecerem na pasta **logs/usuário** no Portal.

**Um script de entrada de exemplo usando o agente:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como posso passar uma entrada no lado, como um arquivo ou arquivo(s) contendo uma tabela de pesquisa, para todos os meus trabalhos?

Construa um [conjunto de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contendo a entrada lateral e registre-o em seu espaço de trabalho. Passe-o para o parâmetro `side_input` do seu `ParallelRunStep`. Além disso, você pode adicionar o caminho na seção `arguments` para acessar facilmente o caminho montado:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Depois disso, você pode acessá-lo em seu script de inferência (por exemplo, em seu método init()) da seguinte maneira:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>Próximas etapas

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-contrib-pipeline](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines com ParallelRunStep e para obter um exemplo de como passar outro arquivo como uma entrada lateral. 
