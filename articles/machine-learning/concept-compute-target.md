---
title: Quais são os alvos da computação
titleSuffix: Azure Machine Learning
description: Defina onde você deseja treinar ou implantar seu modelo com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270414"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quais são os alvos de computação no Azure Machine Learning? 

Um **destino de computação** é um recurso/ambiente de computação designado onde você executa seu script de treinamento ou hospeda sua implantação de serviço. Este local pode ser sua máquina local ou um recurso de computação baseado em nuvem. O uso de metas computacionais facilita que você altere seu ambiente de computação sem ter que alterar seu código.  

Em um ciclo de vida típico de desenvolvimento de modelo, você pode:
1. Comece desenvolvendo e experimentando uma pequena quantidade de dados. Nesta fase, recomendamos seu ambiente local (computador local ou VM baseado em nuvem) como seu alvo de computação. 
2. Dimensione até dados maiores ou faça treinamento distribuído usando um desses alvos de [computação](#train)de treinamento .  
3. Uma vez que seu modelo esteja pronto, implante-o em um ambiente de hospedagem web ou dispositivo IoT com um desses alvos de [computação](#deploy)de implantação .

Os recursos de computação que você usa para seus alvos de computação são anexados a um [espaço de trabalho](concept-workspace.md). Os recursos de cálculo que não sejam a máquina local são compartilhados pelos usuários do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a>Metas de computação de treinamento

O Azure Machine Learning tem suporte variável em diferentes recursos de computação.  Você também pode anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre [como configurar e usar um alvo de computação para treinamento de modelos.](how-to-set-up-training-targets.md)

## <a name="deployment-targets"></a><a name="deploy"></a>Destino de implantação

Os seguintes recursos de computação podem ser usados para hospedar a implantação do seu modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Saiba [onde e como implantar seu modelo em um alvo de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (gerenciado)

Um recurso de computação gerenciado é criado e gerenciado pelo Azure Machine Learning. Este cálculo é otimizado para cargas de trabalho de aprendizado de máquina. Os clusters de computação do Azure Machine Learning e as [instâncias computacionais](concept-compute-instance.md) são os únicos cálculos gerenciados. Recursos adicionais de computação gerenciada podem ser adicionados no futuro.

Você pode criar instâncias de cálculo do Azure Machine Learning (visualização) ou computar clusters em:

| | Azure Machine Learning Studio | Portal do Azure | . | Modelo do Resource Manager | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Instância de computação | sim | sim | sim | sim |  |
| Cluster de computação | sim | sim | sim | sim | sim |

Quando criados, esses recursos de computação são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de metas de computação.

### <a name="compute-clusters"></a>Clusters de cálculo

Você pode usar clusters de computação de aprendizado de máquina do Azure para treinamento e para inferência em lote (visualização).  Com este recurso de computação, você tem:

* Cluster de um ou vários nós
* Escalas automáticas cada vez que você envia uma execução 
* Gerenciamento automático de clusters e agendamento de empregos 
* Suporte para recursos de CPU e GPU



## <a name="unmanaged-compute"></a>Computação não gerenciada

Um alvo de computação não gerenciado *não* é gerenciado pelo Azure Machine Learning. Você cria esse tipo de destino de computação fora do Azure Machine Learning e, em seguida, anexa-o ao seu espaço de trabalho. Os recursos de computação não gerenciados podem exigir etapas adicionais para você manter ou melhorar o desempenho para cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

Saiba como:
* [Configure um alvo de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implante seu modelo para um alvo de computação](how-to-deploy-and-where.md)