---
title: O que são destinos de computação
titleSuffix: Azure Machine Learning
description: Defina onde você deseja treinar ou implantar seu modelo com Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 27c129af9fbf3e76c6c57fbf084596876b51955b
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141918"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>O que são destinos de computação no Azure Machine Learning? 

Um **destino de computação** é um ambiente/recurso de computação designado em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem. O uso de destinos de computação facilita a alteração do ambiente de computação, sem a necessidade de alterar o código.  

Em um ciclo de vida típico de desenvolvimento de modelos, você pode:
1. Comece desenvolvendo e experimentando uma pequena quantidade de dados. Neste estágio, recomendamos seu ambiente local (computador local ou VM baseada em nuvem) como seu destino de computação. 
2. Escale verticalmente para dados maiores ou faça treinamentos distribuídos usando um desses [destinos de computação de treinamento](#train).  
3. Quando o modelo estiver pronto, implante-o em um ambiente de hospedagem da Web ou dispositivo IoT com um desses [destinos de computação de implantação](#deploy).

Os recursos de computação que você usa para seus destinos de computação são anexados a um [espaço de trabalho](concept-workspace.md). Recursos de computação diferentes do computador local são compartilhados por usuários do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a>Treinamento de destinos de computação

Azure Machine Learning tem suporte variado em diferentes recursos de computação.  Você também pode anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre como [configurar e usar um destino de computação para treinamento de modelo](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Destino de implantação

Os recursos de computação a seguir podem ser usados para hospedar sua implantação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Saiba [onde e como implantar seu modelo em um destino de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação Azure Machine Learning (gerenciada)

Um recurso de computação gerenciado é criado e gerenciado pelo Azure Machine Learning. Essa computação é otimizada para cargas de trabalho de Machine Learning. Azure Machine Learning clusters de computação e [instâncias de computação](concept-compute-instance.md) são as únicas computações gerenciadas. Recursos de computação gerenciados adicionais podem ser adicionados no futuro.

Você pode criar Azure Machine Learning instâncias de computação ou clusters de computação de:
* Azure Machine Learning Studio
* Portal do Azure
* Classes [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) e [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) do SDK do Python
* [SDK do R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (visualização)
* Modelo do Resource Manager. Para obter um modelo de exemplo, consulte [criar Azure Machine Learning modelo de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Extensão do Machine Learning [para o CLI do Azure](reference-azure-machine-learning-cli.md#resource-management).  

Quando criados, esses recursos de computação são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de destinos de computação.


|Recurso  |Cluster de computação  |Instância de computação  |
|---------|---------|---------|
|Cluster único ou com vários nós     |    **&check;**       |         |
|Dimensionamento a cada vez que você envia uma execução     |     **&check;**      |         |
|Gerenciamento automático de clusters e agendamento de trabalhos     |   **&check;**        |     **&check;**      |
|Suporte para recursos de CPU e GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quando um cluster de computação está ocioso, ele é dimensionado em escala para 0 nós, portanto, você não paga quando não está em uso.  Uma *instância*de computação, no entanto, é sempre ativada e não faz dimensionamento automático.  Você deve [interromper a instância de computação](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance) quando não a estiver usando para evitar custo extra.

### <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

Ao selecionar um tamanho de nó para um recurso de computação gerenciado no Azure Machine Learning, você pode escolher entre os tamanhos de VM selecionados no Azure. O Azure oferece uma variedade de tamanhos para Linux e Windows para cargas de trabalho diferentes. Consulte aqui para saber mais sobre os diferentes [tipos e tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Há algumas exceções e limitações para escolher um tamanho de VM:
* Não há suporte para algumas séries de VMs no Azure Machine Learning.
* Algumas séries de VMs são restritas. Para usar uma série restrita, entre em contato com o suporte e solicite um aumento de cota para a série. Para obter informações sobre como contatar o suporte, consulte [Opções de suporte do Azure](https://azure.microsoft.com/support/options/)

Consulte a tabela a seguir para saber mais sobre as restrições e as séries com suporte. 

| **Série de VMs com suporte**  | **Restrições** |
|------------|------------|
| D | Nenhum |
| Dv2 | Nenhum |  
| DSv2 | Nenhum |  
| FSv2 | Nenhum | 
| HBv2 | Requer aprovação |  
| HCS | Requer aprovação |  
| M | Requer aprovação |
| NC | Nenhum |    
| NCsv2 | Requer aprovação |
| NCsv3 | Requer aprovação |  
| NDs | Requer aprovação |
| NDv2 | Requer aprovação |
| NV | Nenhum |
| NVv3 | Requer aprovação | 


Embora Azure Machine Learning ofereça suporte a essas séries de VMs, elas podem não estar disponíveis em todas as regiões do Azure. Você pode verificar com a série de VMs disponíveis aqui: [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Computação não gerenciada

Um destino de computação *não gerenciado não* é gerenciado pelo Azure Machine Learning. Você cria esse tipo de destino de computação fora do Azure Machine Learning e, em seguida, anexa-o ao seu espaço de trabalho. Recursos de computação não gerenciados podem exigir etapas adicionais para você manter ou para melhorar o desempenho de cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

Saiba como:
* [Configurar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implantar seu modelo em um destino de computação](how-to-deploy-and-where.md)
