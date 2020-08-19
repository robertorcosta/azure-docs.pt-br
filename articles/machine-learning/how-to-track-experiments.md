---
title: Registrar em log experimentos e métricas de ML
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML e monitore as métricas de execução para aprimorar o processo de criação de modelo. Adicione o log ao script de treinamento usando execute. log, Run. start_logging ou ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7a0fd4178df92cc9102456c1fa2ae4e8927337e4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547317"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Habilitar o registro em log nas execuções de treinamento do Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O SDK do Python Azure Machine Learning permite que você registre informações em tempo real usando o pacote de log do Python padrão e a funcionalidade específica do SDK. Você pode fazer logon localmente e enviar logs para seu espaço de trabalho no Portal.

Os logs podem ajudá-lo a diagnosticar erros e avisos ou rastrear métricas de desempenho como parâmetros e desempenho do modelo. Neste artigo, você aprenderá a habilitar o log nos seguintes cenários:

> [!div class="checklist"]
> * Sessões de treinamento interativo
> * Enviando trabalhos de treinamento usando o ScriptRunConfig
> * Configurações nativas do Python `logging`
> * Registro em log de fontes adicionais


> [!TIP]
> Este artigo mostra como monitorar o processo de treinamento do modelo. Se você estiver interessado em monitorar o uso de recursos e eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [monitoramento Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipos de dados

Você pode registrar vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens, diretórios e muito mais. Para obter mais informações e exemplos de código do Python para diferentes tipos de dados, consulte a [página de referência de classe Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessão de log interativa

As sessões de log interativo normalmente são usadas em ambientes de notebook. O método [experimento. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) inicia uma sessão de log interativa. Todas as métricas registradas durante a sessão são adicionadas ao registro de execução no experimento. O método [Execute. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrunconfig-logs"></a>Logs do ScriptRunConfig

Nesta seção, você aprenderá a adicionar o código de registro em log dentro de execuções do ScriptConfig. Você pode usar a classe [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) para encapsular scripts e ambientes para execuções repetíveis. Você também pode usar essa opção para mostrar um widget Visual Jupyter notebooks para monitoramento.

Este exemplo executa um parâmetro de limpeza em valores Alfa e captura os resultados usando o método [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) .

1. Crie um script de treinamento que inclua a lógica de log, `train.py` .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Envie o ```train.py``` script para ser executado em um ambiente gerenciado pelo usuário. A pasta de script inteira é enviada para treinamento.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    O `show_output` parâmetro ativa o log detalhado, o que permite que você veja os detalhes do processo de treinamento, bem como informações sobre os destinos de computação ou recursos remotos. Use o código a seguir para ativar o log detalhado ao enviar o experimento.

```python
run = exp.submit(src, show_output=True)
```

É possível usar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

Para obter um bloco de anotações de exemplo completo que usa logs do ScriptRunConfigs, consulte [treinar um modelo localmente](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb).

## <a name="native-python-logging"></a>Registro em log do Python nativo

Alguns logs no SDK podem conter um erro que instrui você a definir o nível de log para depurar. Para definir o nível de log, adicione o seguinte código ao script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Fontes de log adicionais

Azure Machine Learning também pode registrar informações de outras fontes durante o treinamento, como execuções automatizadas do Machine Learning ou contêineres do Docker que executam os trabalhos. Esses logs não estão documentados, mas se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles poderão usar esses logs durante a solução de problemas.

Para obter informações sobre as métricas de log no designer de Azure Machine Learning (versão prévia), consulte [como registrar métricas no designer (versão prévia)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Blocos de anotações de exemplo
Os seguintes blocos de anotações demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte estes artigos para saber mais sobre como usar Azure Machine Learning:

* Saiba como [registrar métricas no designer de Azure Machine Learning (versão prévia)](how-to-track-designer-experiments.md).

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).
