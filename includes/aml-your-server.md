---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/26/2019
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841115"
---
1. Use as instruções em [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar o SDK do Azure Machine Learning para Python

1. Criar um [workspace do Serviço do Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Escrever um [arquivo de configuração](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de notebook do seu diretório clonado.

    ```shell
    jupyter notebook
    ```
