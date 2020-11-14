---
title: Como depurar e solucionar problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas do ParallelRunStep em pipelines do Machine Learning no SDK do Azure Machine Learning para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 09f75e9e8f972ec44098e119dc5b30bd44638918
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630457"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Como depurar e solucionar problemas de ParallelRunStep


Neste artigo, você aprenderá a depurar e solucionar problemas da classe [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) do [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a seção [Testar scripts localmente](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) para pipelines do Machine Learning. Seu ParallelRunStep é executado como uma etapa em pipelines de ML para que a mesma resposta se aplique a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurar scripts do contexto remoto

A transição da depuração de um script de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um passo difícil. Para obter informações sobre como localizar seus logs no portal, a seção [Pipelines do Machine Learning em scripts de depuração de um contexto remoto](how-to-debug-pipelines.md). As informações contidas nessa seção também se aplicam a um ParallelRunStep.

Por exemplo, o arquivo de log `70_driver_log.txt` contém informações do controlador que inicia o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos do ParallelRunStep, há logs de várias fontes diferentes. No entanto, são criados dois arquivos consolidados que fornecem informações de alto nível:

- `~/logs/job_progress_overview.txt`: Esse arquivo fornece informações de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até o momento e o número de mini-lotes processados até o momento. Nesse final, ele mostra o resultado do trabalho. Se o trabalho tiver falhado, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/sys/master_role.txt`: Esse arquivo fornece a exibição do nó principal (também conhecido como orquestrador) do trabalho em execução. Inclui a criação de tarefas, o monitoramento de progresso, o resultado da execução.

Os logs gerados do script de entrada usando o auxiliar EntryScript e as instruções PRINT serão encontrados nos seguintes arquivos:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Esses arquivos são os logs gravados de entry_script usando o auxiliar EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Esses arquivos são os logs de stdout (por exemplo, a instrução print) de entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Esses arquivos são os logs de stderr de entry_script.

Para uma compreensão concisa dos erros em seu script, há:

- `~/logs/user/error.txt`: Este arquivo tentará resumir os erros em seu script.

Para obter mais informações sobre erros no seu script, há:

- `~/logs/user/error/`: Contém rastreamentos de pilha completa de exceções geradas durante o carregamento e a execução do script de entrada.

Quando você precisar de um entendimento completo de como cada nó executou o script de pontuação, examine os logs de processo individuais para cada nó. Os logs de processo podem ser encontrados na pasta `sys/node`, agrupados por nós de trabalho:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Esse arquivo fornece informações detalhadas sobre cada mini-lote conforme ele é selecionado ou concluído por um trabalho. Para cada mini-lote, esse arquivo inclui:

    - O endereço IP e o PID do processo de trabalho. 
    - O número total de itens, contagem de itens processados com êxito e contagem de itens com falha.
    - A hora de início, a duração, o tempo de processamento e o tempo do método de execução.

Você também pode encontrar informações sobre o uso de recursos dos processos para cada trabalho. Essas informações estão no formato CSV e estão localizadas em `~/logs/sys/perf/<ip_address>/node_resource_usage.csv`. As informações sobre cada processo estão disponíveis em `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como fazer log do meu script de usuário a partir de um contexto remoto?
ParallelRunStep pode executar vários processos em um nó com base em process_count_per_node. Para organizar os logs de cada processo no nó e combinar a instrução print e log, é recomendável usar o ParallelRunStep Logger, conforme mostrado abaixo. Você Obtém um agente de log de EntryScript e faz os logs aparecerem na pasta **logs/usuário** no Portal.

**Um script de entrada de exemplo usando o agente:**
```python
from azureml_user.parallel_run import EntryScript

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

O usuário pode passar dados de referência para script usando side_inputs parâmetro de ParalleRunStep. Todos os conjuntos de itens fornecidos como side_inputs serão montados em cada nó de trabalho. O usuário pode obter o local de montagem passando o argumento.

Construa um [conjunto](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) de dados que contenha o dado de referência e registre-o com seu espaço de trabalho. Passe-o para o parâmetro `side_inputs` do seu `ParallelRunStep`. Além disso, você pode adicionar seu caminho na `arguments` seção para acessar facilmente seu caminho montado:

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

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Como usar conjuntos de dados de entrada com autenticação de entidade de serviço?

O usuário pode passar conjuntos de dados de entrada com a autenticação de entidade de serviço usada no espaço de trabalho. O uso desse conjunto de ParallelRunStep requer que o conjunto de um seja registrado para que ele construa a configuração do ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>Próximas etapas

_ Veja estes [blocos de anotações do Jupyter que demonstram pipelines Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-pipeline-Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) . Exiba a [documentação](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) de referência para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines com o ParallelRunStep. O tutorial mostra como passar outro arquivo como uma entrada lateral.