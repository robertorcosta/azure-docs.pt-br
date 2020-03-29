---
title: Depurar e solucionar problemas ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depurar e solucionar problemas ParallelRunStep em pipelines de aprendizado de máquina no Azure Machine Learning SDK for Python. Aprenda armadilhas comuns para desenvolver com pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122958"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depurar e solucionar problemas ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como depurar e solucionar problemas da classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testando scripts localmente

Consulte a [seção De testes localmente para](how-to-debug-pipelines.md#testing-scripts-locally) obter pipelines de aprendizado de máquina. O ParallelRunStep é executado como um passo nos pipelines ML para que a mesma resposta se aplique a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depuração de scripts de contexto remoto

A transição de depurar um script de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre como encontrar seus logs no portal, a [seção de pipelines de aprendizado de máquina sobre a depuração de scripts a partir de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações nessa seção também se aplicam a uma etapa paralela.

Por exemplo, o `70_driver_log.txt` arquivo de log contém informações do controlador que inicia o código de etapa de execução paralelo.

Devido à natureza distribuída de trabalhos paralelos, existem registros de várias fontes diferentes. No entanto, dois arquivos consolidados são criados que fornecem informações de alto nível:

- `~/logs/overview.txt`: Este arquivo fornece uma informação de alto nível sobre o número de minilotes (também conhecidos como tarefas) criados até agora e o número de minilotes processados até agora. Neste final, mostra o resultado do trabalho. Se o trabalho falhar, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/sys/master.txt`: Este arquivo fornece a visão do nó mestre (também conhecido como orquestrador) do trabalho em execução. Inclui criação de tarefas, monitoramento de progresso, resultado da execução.

Os logs gerados a partir do script de entrada usando EntryScript.logger e instruções de impressão serão encontrados nos seguintes arquivos:

- `~/logs/user/<ip_address>/Process-*.txt`: Este arquivo contém registros escritos de entry_script usando EntryScript.logger. Também contém declaração de impressão (stdout) de entry_script.

Quando você precisar de uma compreensão completa de como cada nó executou o script de pontuação, olhe os logs de processo individuais para cada nó. Os registros do processo podem `sys/worker` ser encontrados na pasta, agrupados por nós do trabalhador:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Este arquivo fornece informações detalhadas sobre cada minilote à medida que é recolhido ou concluído por um trabalhador. Para cada minilote, este arquivo inclui:

    - O endereço IP e o PID do processo do trabalhador. 
    - O número total de itens, a contagem de itens processados com sucesso e a contagem de itens com falha.
    - O tempo de início, duração, tempo de processo e tempo de execução do método.

Você também pode encontrar informações sobre o uso de recursos dos processos para cada trabalhador. Essas informações estão no formato CSV `~/logs/sys/perf/<ip_address>/`e estão localizadas em . Para um único nó, os arquivos `~logs/sys/perf`de trabalho estarão disponíveis em . Por exemplo, ao verificar a utilização de recursos, veja os seguintes arquivos:

- `Process-*.csv`: Utilização de recursos por processo do trabalhador. 
- `sys.csv`: Por registro de nó.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como faço login do meu script de usuário a partir de um contexto remoto?
Você pode obter um logger do EntryScript como mostrado no código de amostra abaixo para fazer com que os logs apareçam em **logs/pasta de usuário** no portal.

**Um script de entrada de exemplo usando o logger:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como eu poderia passar uma entrada lateral, como, um arquivo ou arquivo(s) contendo uma tabela de análise, para todos os meus trabalhadores?

Construa um objeto [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contendo a entrada lateral e registre-se com seu espaço de trabalho. Depois disso, você pode acessá-lo em seu script de inferência (por exemplo, no seu método init() da seguinte forma:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Próximas etapas

* Consulte a referência sdk para obter ajuda com o pacote [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre o uso de pipelines com etapa de execução paralela.
