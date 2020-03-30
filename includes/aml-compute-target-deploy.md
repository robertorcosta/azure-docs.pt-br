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
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122569"
---
| Destino de computação | Usado para | Suporte à GPU | Suporte fpga | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;&nbsp;web local](../articles/machine-learning/how-to-deploy-and-where.md#local) | Teste/depuração | &nbsp; | &nbsp; | Use para testes limitados e solução de problemas. A aceleração do hardware depende do uso de bibliotecas no sistema local.
| [Serviço web&nbsp;&nbsp;de exemplo de computação do Azure Machine Learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Teste/depuração | &nbsp; | &nbsp; | Use para testes limitados e solução de problemas.
| [Serviço Azure Kubernetes (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação de serviçoweb) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Uso para implantações de produção em alta escala. Fornece tempo de resposta rápido e autodimensionamento do serviço implantado. O autoscaling de cluster não é suportado através do Azure Machine Learning SDK. Para alterar os nós no cluster AKS, use a ui para o cluster AKS no portal Azure. AKS é a única opção disponível para o designer. |
| [Instâncias de Contêiner do Azure](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Use para cargas de trabalho baseadas em CPU de baixa escala que requerem menos de 48 GB de RAM. |
| [Clusters de cálculo do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Visualização) Inferência em lote&nbsp; | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de aprendizagem de máquina) | &nbsp;  | Executar pontuação em lote em computação sem servidor. Suporta VMs normais e de baixa prioridade. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Visualização) Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Visualização) Módulo IoT&nbsp; |  &nbsp; | &nbsp; | Implantar e servir modelos ML em dispositivos IoT. |
| [Borda da caixa de dados do Azure](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implantar e servir modelos ML em dispositivos IoT. |

> [!NOTE]
> Embora os alvos de computação como local, a instância de computação do Azure Machine Learning e os clusters de computação do Azure Machine Learning ofereçam suporte à GPU para treinamento e experimentação, o uso de GPU para inferência __quando implantado como serviço web__ é suportado apenas no Azure Kubernetes Service.
>
> O uso de uma GPU para inferência __ao pontuar com um pipeline de aprendizado de máquina__ é suportado apenas no Azure Machine Learning Compute.