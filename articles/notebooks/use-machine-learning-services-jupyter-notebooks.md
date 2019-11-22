---
title: Usar Azure Machine Learning no Azure Notebooks
description: Uma visão geral dos blocos de anotações de exemplo para Azure Machine Learning que você pode usar com Azure Notebooks.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277450"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Usar Azure Machine Learning em um bloco de anotações

Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com [Azure Machine Learning](/azure/machine-learning/service/). Você pode clonar facilmente um projeto de exemplo em sua conta do Notebooks para explorar uma variedade de cenários do Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo em sua conta

1. Entre no [Azure Notebooks](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. No pop-up, insira `Azure/MachineLearningNotebooks` no **repositório GitHub**, forneça um nome para o projeto **no nome do projeto** , como "Azure Machine Learning", forneça um identificador na ID do **projeto**, desmarque **público** se desejar e, em seguida, selecione **importar**.

    ![Importar exemplo de Notebook do Azure Machine Learning para a conta do Notebooks](media/azureml-import-project.png)

1. Após um minuto ou dois, o Azure Notebooks acessará automaticamente o painel do novo projeto.

## <a name="run-a-sample-notebook"></a>Executar um exemplo de notebook

1. Selecione **00 – configuration.ipynb** para iniciar a seção de configuração do notebook e siga as instruções para criar um Workspace do Azure Machine Learning.

    - Como o Azure Notebooks já contém os pacotes Python necessários, basta executar o snippet de código na etapa 2 dos Pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Próximas etapas

A documentação Azure Machine Learning contém uma variedade de outros recursos que orientam você durante o trabalho com Machine Learning em blocos de anotações:

- [Início rápido: Use o Python para começar a usar o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutorial #1: treinar um modelo de classificação de imagem com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutorial #2: implantar um modelo de classificação de imagem na instância de contêiner do Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: treinar um modelo de classificação com o aprendizado de máquina automatizado no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Confira também a documentação do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
