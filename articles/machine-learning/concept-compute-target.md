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
ms.date: 09/29/2020
ms.openlocfilehash: ca23bb49a3592dcc139bcc04875f3867018e158d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707720"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>O que são destinos de computação no Azure Machine Learning? 

Um **destino de computação** é um ambiente/recurso de computação designado em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem. O uso de destinos de computação facilita a alteração do ambiente de computação, sem a necessidade de alterar o código.  

Em um ciclo de vida típico de desenvolvimento de modelos, você pode:
1. Comece desenvolvendo e experimentando uma pequena quantidade de dados. Neste estágio, recomendamos seu ambiente local (computador local ou VM baseada em nuvem) como seu destino de computação. 
2. Escale verticalmente para dados maiores ou faça treinamentos distribuídos usando um desses [destinos de computação de treinamento](#train).  
3. Quando o modelo estiver pronto, implante-o em um ambiente de hospedagem da Web ou dispositivo IoT com um desses [destinos de computação de implantação](#deploy).

Os recursos de computação que você usa para seus destinos de computação são anexados a um [espaço de trabalho](concept-workspace.md). Recursos de computação diferentes do computador local são compartilhados por usuários do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a> Treinamento de destinos de computação
O Azure Machine Learning tem suporte variado nos diferentes destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou realiza treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre como [Enviar uma execução de treinamento para um destino de computação](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Destinos de computação para inferência

Os recursos de computação a seguir podem ser usados para hospedar sua implantação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Ao executar a inferência, Azure Machine Learning cria um contêiner do Docker que hospeda o modelo e os recursos associados necessários para usá-lo. Esse contêiner é usado em um dos seguintes cenários de implantação:

* Como um __serviço Web__ que é usado para inferência em tempo real. As implantações de serviço Web usam um dos seguintes destinos de computação:

    * [Computador local](how-to-attach-compute-targets.md#local)
    * [Instância de computação do Azure Machine Learning](how-to-create-manage-compute-instance.md)
    * [Instâncias de Contêiner do Azure](how-to-attach-compute-targets.md#aci)
    * [Serviço de Kubernetes do Azure](how-to-create-attach-kubernetes.md)
    * Azure Functions (versão prévia). A implantação em Azure Functions se baseia apenas em Azure Machine Learning para criar o contêiner do Docker. A partir daí, ele é implantado usando Azure Functions. Para obter mais informações, consulte [implantar um modelo de aprendizado de máquina para Azure Functions (versão prévia)](how-to-deploy-functions.md).

* Como um ponto de extremidade de __inferência de lote__ que é usado para processar periodicamente lotes de dados. As inferências em lote usam [Azure Machine Learning cluster de computação](how-to-create-attach-compute-cluster.md).

* Para um __dispositivo IOT__ (versão prévia). A implantação em um dispositivo IoT conta apenas com Azure Machine Learning para criar o contêiner do Docker. A partir daí, ele é implantado usando Azure IoT Edge. Para obter mais informações, consulte [implantar como um módulo IOT Edge (versão prévia)](/azure/iot-edge/tutorial-deploy-machine-learning).

Saiba [onde e como implantar seu modelo em um destino de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação Azure Machine Learning (gerenciada)

Um recurso de computação gerenciado é criado e gerenciado pelo Azure Machine Learning. Essa computação é otimizada para cargas de trabalho de Machine Learning. Azure Machine Learning clusters de computação e [instâncias de computação](concept-compute-instance.md) são as únicas computações gerenciadas. 

Você pode criar Azure Machine Learning instâncias de computação ou clusters de computação de:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* SDK e CLI do Python:
    * [Instância de computação](how-to-create-manage-compute-instance.md)
    * [Cluster de computação](how-to-create-attach-compute-cluster.md)
* [SDK do R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (visualização)
* Modelo do Resource Manager. Para obter um modelo de exemplo, consulte [criar Azure Machine Learning modelo de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Extensão do Machine Learning [para o CLI do Azure](reference-azure-machine-learning-cli.md#resource-management).  

Quando criados, esses recursos de computação são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de destinos de computação.


|Funcionalidade  |Cluster de computação  |Instância de computação  |
|---------|---------|---------|
|Cluster único ou com vários nós     |    **&check;**       |         |
|Dimensionamento a cada vez que você envia uma execução     |     **&check;**      |         |
|Gerenciamento automático de clusters e agendamento de trabalhos     |   **&check;**        |     **&check;**      |
|Suporte para recursos de CPU e GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quando um cluster de computação está ocioso, ele é dimensionado em escala para 0 nós, portanto, você não paga quando não está em uso.  Uma *instância*de computação, no entanto, é sempre ativada e não faz dimensionamento automático.  Você deve [interromper a instância de computação](how-to-create-manage-compute-instance.md#manage) quando não a estiver usando para evitar custo extra. 

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
| Dv3 | Nenhum|
| DSv2 | Nenhum | 
| DSv3 | Nenhum|
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

### <a name="compute-isolation"></a>Isolamento de computação

Azure Machine Learning computação oferece tamanhos de máquina virtual que são isolados para um tipo de hardware específico e dedicados a um único cliente. Os tamanhos de máquinas virtuais isoladas são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de cargas de trabalho de outros clientes por motivos que incluem requisitos de conformidade e regulamentação de atendimento. Utilizar um tamanho isolado garante que sua máquina virtual será apenas sendo executada na instância de servidor específico.

As ofertas atuais da máquina virtual isolada incluem:
* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*Compatível com RDMA

Consulte aqui para saber mais sobre [o isolamento na nuvem pública do Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices).

## <a name="unmanaged-compute"></a>Computação não gerenciada

Um destino de computação *não gerenciado não* é gerenciado pelo Azure Machine Learning. Você cria esse tipo de destino de computação fora do Azure Machine Learning e, em seguida, anexa-o ao seu espaço de trabalho. Recursos de computação não gerenciados podem exigir etapas adicionais para você manter ou para melhorar o desempenho de cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

Saiba como:
* [Usar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implantar seu modelo em um destino de computação](how-to-deploy-and-where.md)
