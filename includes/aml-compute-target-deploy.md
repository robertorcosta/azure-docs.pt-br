---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841929"
---
O destino de computação que você usa para hospedar seu modelo afetará o custo e a disponibilidade do ponto de extremidade implantado. Use esta tabela para escolher um destino de computação apropriado.

| Destino de computação | Usado para | Suporte de GPU | Suporte de FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;Web&nbsp;local](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados. A aceleração de hardware depende do uso de bibliotecas no sistema local.
| [Serviço Web de instância de computação do Azure&nbsp;Machine&nbsp;Learning](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados.
| [AKS (Serviço de Kubernetes do Azure)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação do serviço Web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Use para implantações de produção em grande escala. Fornece tempo de resposta rápido e dimensionamento automático do serviço implantado. O dimensionamento automático do cluster não tem suporte por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster do AKS, use a interface do usuário do cluster do AKS no portal do Azure. O AKS é a única opção disponível para o designer. |
| [Instâncias de Contêiner do Azure](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Use para cargas de trabalho baseadas em CPU de baixa escala que exigem menos de 48 GB de RAM. |
| [Clusters de cálculo do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Inferência&nbsp;de lote | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de machine learning) | &nbsp;  | Executar a pontuação de lote em computação sem servidor. Compatível com VMs normais e de baixa prioridade. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Versão prévia) Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Versão prévia) Módulo do&nbsp;IoT |  &nbsp; | &nbsp; | Implante e ofereça modelos de machine learning em dispositivos IoT. |
| [Borda da caixa de dados do Azure](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implante e ofereça modelos de machine learning em dispositivos IoT. |

> [!NOTE]
> Embora os destinos de computação como local, Computação do Azure Machine Learning e clusters de cálculo do Azure Machine Learning sejam compatíveis com GPU para treinamento e experimentação, o uso de GPU para inferência _quando implantada como um serviço Web_ só é compatível com o AKS.
>
> O uso de uma GPU para inferência _quando a pontuação é feita com um pipeline de machine learning_ só é compatível com a Computação do Azure Machine Learning.

> [!NOTE]
> * As instâncias de contêiner são adequadas somente para modelos pequenos com menos de 1 GB de tamanho.
> * Use clusters do AKS de nó único para desenvolvimento/teste de modelos maiores.