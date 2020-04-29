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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122569"
---
| Destino de computação | Usada para | Suporte de GPU | Suporte do FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;Web&nbsp;local](../articles/machine-learning/how-to-deploy-and-where.md#local) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados. A aceleração de hardware depende do uso de bibliotecas no sistema local.
| [Serviço Web&nbsp;de&nbsp;instância de computação Azure Machine Learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados.
| [AKS (Serviço de Kubernetes do Azure)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação de serviço Web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Use para implantações de produção de grande escala. Fornece tempo de resposta rápido e dimensionamento automático do serviço implantado. O dimensionamento automático do cluster não tem suporte por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster AKS, use a interface do usuário do cluster do AKS no portal do Azure. AKS é a única opção disponível para o designer. |
| [Instâncias de Contêiner do Azure](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Use para cargas de trabalho baseadas em CPU de baixa escala que exigem menos de 48 GB de RAM. |
| [Clusters de cálculo do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Apresentação Inferência de lote&nbsp; | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de Machine Learning) | &nbsp;  | Executar a pontuação de lote em computação sem servidor. Dá suporte a VMs normais e de baixa prioridade. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Apresentação Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Apresentação Módulo&nbsp;IOT |  &nbsp; | &nbsp; | Implante e sirva modelos de ML em dispositivos IoT. |
| [Borda da caixa de dados do Azure](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implante e sirva modelos de ML em dispositivos IoT. |

> [!NOTE]
> Embora os destinos de computação, como local, Azure Machine Learning instância de computação, e Azure Machine Learning clusters de computação ofereçam suporte à GPU para treinamento e experimentação, usar GPU para inferência __quando implantado como um serviço Web__ tem suporte apenas no serviço kubernetes do Azure.
>
> Usar uma GPU para inferência __quando a pontuação com um pipeline de aprendizado de máquina__ tem suporte apenas em Azure Machine Learning computação.