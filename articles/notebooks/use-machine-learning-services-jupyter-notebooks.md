---
title: Usar os Serviços do Azure Machine Learning no Azure Notebooks
description: Uma visão geral dos notebooks de exemplo dos Serviços do Azure Machine Learning que você pode usar com o Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973022"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Usar o Serviço do Azure Machine Learning em um notebook

O Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com o [Serviço do Azure Machine Learning](/azure/machine-learning/service/). Você pode clonar facilmente um projeto de exemplo em sua conta do Notebooks para explorar uma variedade de cenários do Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo em sua conta

1. Entre no [Azure Notebooks](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. Na janela pop-up, insira `Azure/MachineLearningNotebooks` no **Repositório do GitHub**, forneça um nome para o projeto em **Nome do Projeto**, como "Serviço do Azure Machine Learning", forneça um identificador em **ID do Projeto**, desmarque **Público** se desejar e, em seguida, selecione **Importar**.

    ![Importar exemplo de Notebook do Azure Machine Learning para a conta do Notebooks](media/azureml-import-project.png)

1. Após um minuto ou dois, o Azure Notebooks acessará automaticamente o painel do novo projeto.

## <a name="run-a-sample-notebook"></a>Executar um exemplo de notebook

1. Selecione **00 – configuration.ipynb** para iniciar a seção de configuração do notebook e siga as instruções para criar um Workspace do Azure Machine Learning.

    - Como o Azure Notebooks já contém os pacotes Python necessários, basta executar o snippet de código na etapa 2 dos Pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Próximas etapas

A documentação dos Serviços do Azure Machine Learning contém uma variedade de outros recursos que orientam o trabalho com os Serviços do Machine Learning nos notebooks:

- [Início Rápido: Usar o Python para começar a trabalhar com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutorial nº 1: Treinar um modelo de classificação de imagem com o Serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutorial nº 2: Implantar um modelo de classificação de imagem na ACI (Instância de Contêiner do Azure)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: Treinar um modelo de classificação com aprendizado de máquina automatizado no Serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Confira também a documentação do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
