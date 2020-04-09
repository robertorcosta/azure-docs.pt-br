---
title: Inicie, monitore e cancele as corridas de treinamento em Python
titleSuffix: Azure Machine Learning
description: Aprenda a iniciar, definir o status de, marcar e organizar seus experimentos de aprendizagem de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: c1b70aaef49cc2b993c873509dc935d71069efa2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985908"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Inicie, monitore e cancele as corridas de treinamento em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O [Azure Machine Learning SDK for Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [Machine Learning CLI](reference-azure-machine-learning-cli.md)e a [zure Machine Learning studio](https://ml.azure.com) fornecem vários métodos para monitorar, organizar e gerenciar suas corridas para treinamento e experimentação.

Este artigo mostra exemplos das seguintes tarefas:

* Monitore o desempenho da execução.
* Cancelar ou falhar corridas.
* Criar corridas de crianças.
* Marque e encontre corridas.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes itens:

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizado de máquina do Azure.](how-to-manage-workspace.md)

* O Azure Machine Learning SDK para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [Instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Para verificar a sua versão do Azure Machine Learning SDK, use o seguinte código:

    ```python
    print(azureml.core.VERSION)
    ```

* A extensão [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e [CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Inicie uma corrida e seu processo de registro

### <a name="using-the-sdk"></a>Usar o SDK

Configure seu experimento importando as classes [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [Experiment,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) do pacote [azureml.core.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Inicie uma corrida e seu [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) processo de registro com o método.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Usando a CLI

Para iniciar uma execução do seu experimento, use as seguintes etapas:

1. A partir de um shell ou prompt de comando, use o Azure CLI para autenticar a sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Anexar uma configuração de espaço de trabalho à pasta que contém seu script de treinamento. Substitua pelo `myworkspace` espaço de trabalho azure Machine Learning. Substitua pelo `myresourcegroup` grupo de recursos Do Azure que contém seu espaço de trabalho:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando `.azureml` cria um subdiretório que contém arquivos de ambiente runconfig e conda de exemplo. Ele também `config.json` contém um arquivo que é usado para se comunicar com o seu espaço de trabalho Azure Machine Learning.

    Para obter mais informações, consulte [a pasta az ml attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Para iniciar a corrida, use o seguinte comando. Ao usar este comando, especifique o nome \*do arquivo runconfig (o texto antes de .runconfig se você estiver olhando para o sistema de arquivos) contra o parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando criou `.azureml` um subdiretório, que contém dois exemplos de arquivos runconfig.
    >
    > Se você tiver um script Python que crie um objeto de configuração de execução programáticamente, você pode usar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
    >
    > Para obter mais exemplos de [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)arquivos runconfig, consulte .

    Para obter mais informações, consulte [az ml executar o script de envio](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio azure Machine Learning

Para iniciar uma execução de pipeline no designer (visualização), use as seguintes etapas:

1. Defina um alvo de computação padrão para o seu pipeline.

1. Selecione **Executar** na parte superior da tela do gasoduto.

1. Selecione um experimento para agrupar suas corridas de pipeline.

## <a name="monitor-the-status-of-a-run"></a>Monitore o status de uma corrida

### <a name="using-the-sdk"></a>Usar o SDK

Obtenha o status de [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) uma corrida com o método.

```python
print(notebook_run.get_status())
```

Para obter o ID de execução, o tempo [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) de execução e detalhes adicionais sobre a execução, use o método.

```python
print(notebook_run.get_details())
```

Quando a corrida terminar com [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) sucesso, use o método para marcá-la como concluída.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Se você usar `with...as` o padrão de design do Python, a execução se marcará automaticamente como concluída quando a execução estiver fora do escopo. Você não precisa marcar manualmente a corrida como concluída.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Usando a CLI

1. Para visualizar uma lista de corridas para o seu experimento, use o seguinte comando. Substitua pelo `experiment` nome do seu experimento:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Este comando retorna um documento JSON que lista informações sobre corridas para este experimento.

    Para obter mais informações, consulte [a lista de experimentos az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Para exibir informações sobre uma execução específica, use o seguinte comando. Substitua pelo `runid` ID da execução:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Este comando retorna um documento JSON que lista informações sobre a execução.

    Para obter mais informações, consulte [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio azure Machine Learning

Para ver o número de corridas ativas para sua experiência no estúdio.

1. Navegue até a seção **Experimentos..** 

1. Selecione um experimento.

    Na página do experimento, você pode ver o número de metas ativas de computação e a duração de cada execução. 

1. Selecione um número de execução específico.

1. Na guia **Logs,** você pode encontrar registros de diagnóstico e erro para a execução do pipeline.


## <a name="cancel-or-fail-runs"></a>Cancelar ou falhar executa

Se você notar um erro ou se sua corrida está demorando muito para terminar, você pode cancelar a corrida.

### <a name="using-the-sdk"></a>Usar o SDK

Para cancelar uma execução usando o [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) SDK, use o método:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Se a execução terminar, mas contiver um erro (por exemplo, o [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) script de treinamento incorreto foi usado), você pode usar o método para marcá-lo como falha.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Usando a CLI

Para cancelar uma execução usando o CLI, use o seguinte comando. Substitua pelo `runid` ID da execução

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Para obter mais informações, consulte [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio azure Machine Learning

Para cancelar uma execução no estúdio, usando as seguintes etapas:

1. Vá para o oleoduto em execução na seção **Experimentos** ou **Pipelines.** 

1. Selecione o número de execução do pipeline que deseja cancelar.

1. Na barra de ferramentas, selecione **Cancelar**


## <a name="create-child-runs"></a>Criar corridas de crianças

Criar corridas de crianças para agrupar corridas relacionadas, como para diferentes iterações de ajuste de hiperparâmetro.

> [!NOTE]
> As corridas de crianças só podem ser criadas usando o SDK.

Este exemplo de `hello_with_children.py` código usa o script para criar um lote de [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) cinco crianças executadas dentro de uma execução enviada usando o método:

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> À medida que saem do escopo, as corridas de crianças são automaticamente marcadas como concluídas.

Para criar muitas corridas de [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) crianças de forma eficiente, use o método. Como cada criação resulta em uma chamada de rede, criar um lote de corridas é mais eficiente do que criá-los um por um.

### <a name="submit-child-runs"></a>Enviar corridas de crianças

As corridas de crianças também podem ser enviadas a partir de uma corrida dos pais. Isso permite que você crie hierarquias de corridas de pais e filhos. 

Você pode desejar que seu filho execute para usar uma configuração de execução diferente da execução dos pais. Por exemplo, você pode usar uma configuração menos poderosa, baseada em CPU para o pai, enquanto usa configurações baseadas em GPU para seus filhos. Outro desejo comum é passar a cada filho diferentes argumentos e dados. Para personalizar a corrida `RunConfiguration` de uma criança, `ScriptRunConfig` passe um objeto para o construtor da criança. Este exemplo de código, que `ScriptRunConfig` seria parte do script do objeto pai:

- Cria `RunConfiguration` uma recuperação de um recurso de computação nomeado`"gpu-compute"`
- Itera sobre diferentes valores de `ScriptRunConfig` argumento a serem passados para os objetos das crianças
- Cria e envia uma nova execução de filhos, usando o recurso e argumento de computação personalizado
- Blocos até que toda a criança corra completa

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Para criar muitas corridas de crianças com configurações, argumentos [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) e entradas idênticas de forma eficiente, use o método. Como cada criação resulta em uma chamada de rede, criar um lote de corridas é mais eficiente do que criá-los um por um.

Dentro de uma corrida de crianças, você pode visualizar o ID de execução dos pais:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Criança consulta executada

Para consultar as corridas de um pai [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) específico, use o método. O ``recursive = True`` argumento permite que você consulta uma árvore aninhada de filhos e netos.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Marcar e encontrar corridas

No Azure Machine Learning, você pode usar propriedades e tags para ajudar a organizar e consultar suas corridas para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicionar propriedades e tags

#### <a name="using-the-sdk"></a>Usar o SDK

Para adicionar metadados pesquisáveis às [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) suas corridas, use o método. Por exemplo, o código `"author"` a seguir adiciona a propriedade à execução:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

As propriedades são imutáveis, por isso criam um registro permanente para fins de auditoria. O exemplo de código a seguir resulta em um erro, porque já adicionamos `"azureml-user"` como o valor da `"author"` propriedade no código anterior:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Ao contrário das propriedades, as tags são mutáveis. Para adicionar informações pesquisáveis e significativas para [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) os consumidores de seu experimento, use o método.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Você também pode adicionar tags de seqüência simples. Quando essas tags aparecem no dicionário de tags `None`como chaves, elas têm um valor de .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Usando a CLI

> [!NOTE]
> Usando o CLI, você só pode adicionar ou atualizar tags.

Para adicionar ou atualizar uma tag, use o seguinte comando:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Para obter mais informações, consulte [a atualização de execução do az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Propriedades e tags de consulta

Você pode consultar executados dentro de um experimento para retornar uma lista de executações que correspondem a propriedades e tags específicas.

#### <a name="using-the-sdk"></a>Usar o SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Usando a CLI

O Azure CLI suporta consultas [JMESPath,](http://jmespath.org) que podem ser usadas para filtrar corridas com base em propriedades e tags. Para usar uma consulta JMESPath com o Azure `--query` CLI, especifique-a com o parâmetro. Os exemplos a seguir mostram consultas básicas usando propriedades e tags:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Para obter mais informações sobre a consulta aos resultados do Azure CLI, consulte [Asaída de comando CLI do Consulta Azure](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio azure Machine Learning

1. Navegue até a seção **Pipelines.**

1. Use a barra de pesquisa para filtrar pipelines usando tags, descrições, nomes de experimentos e nome do submetida.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Os cadernos a seguir demonstram os conceitos deste artigo:

* Para saber mais sobre as APIs de registro, consulte o [notebook aPI de registro](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Para obter mais informações sobre o gerenciamento de corridas com o Azure Machine Learning SDK, consulte o [notebook gerenciar executa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Próximas etapas

* Para saber como registrar métricas para seus experimentos, consulte [métricas de log durante as corridas de treinamento](how-to-track-experiments.md).
* Para saber como monitorar recursos e registros do Azure Machine Learning, consulte [Monitorando o Aprendizado de Máquina do Azure](monitor-azure-machine-learning.md).