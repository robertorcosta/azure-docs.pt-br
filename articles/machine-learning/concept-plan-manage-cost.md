---
title: Planejar e gerenciar custos
titleSuffix: Azure Machine Learning
description: Planeje e gerencie custos para Azure Machine Learning com análise de custo no portal do Azure. Ao criar modelos de aprendizado de máquina, saiba mais dicas de economia de custos para reduzir seu custo.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: e8554bf4385c7ddad540b8ba807988618fe80b53
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322984"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planejar e gerenciar custos para Azure Machine Learning

Este artigo descreve como planejar e gerenciar custos para Azure Machine Learning. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos antes de adicionar qualquer recurso. Em seguida, ao adicionar os recursos do Azure, examine os custos estimados. Por fim, use as dicas de economia de custos ao treinar seu modelo com clusters de computação Azure Machine Learning gerenciados.

Depois de começar a usar Azure Machine Learning recursos, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Examine também os custos previstos e identifique as tendências de gastos para identificar as áreas em que talvez você queira agir.

Entenda que os custos de Azure Machine Learning são apenas uma parte dos custos mensais em sua fatura do Azure. Se estiver usando outros serviços do Azure, você será cobrado por todos os serviços e recursos do Azure usados na sua assinatura do Azure, incluindo os serviços de terceiros. Este artigo explica como planejar e gerenciar custos para Azure Machine Learning. Depois que você estiver familiarizado com o gerenciamento de custos para Azure Machine Learning, aplique métodos semelhantes para gerenciar os custos de todos os serviços do Azure usados em sua assinatura.

Ao treinar seus modelos de aprendizado de máquina, use os clusters de computação Azure Machine Learning gerenciados para tirar proveito de mais dicas de economia:

* Configurar seus clusters de treinamento para dimensionamento automático
* Definir cotas em sua assinatura e espaços de trabalho
* Definir políticas de encerramento em sua execução de treinamento
* Usar VMS (máquinas virtuais) de baixa prioridade
* Usar uma instância de VM reservada do Azure

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. 

Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Estimar custos

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar os recursos em uma conta de Azure Machine Learning. À esquerda, selecione **ia + Machine Learning** e, em seguida, selecione **Azure Machine Learning** para começar.  

A captura de tela a seguir mostra a estimativa de custo usando a calculadora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custo na calculadora do Azure":::

Ao adicionar novos recursos ao seu espaço de trabalho, retorne a esta calculadora e adicione o mesmo recurso aqui para atualizar suas estimativas de custo.

Para obter mais informações, consulte [preços de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Obter alertas de custo

Crie [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerenciar custos e crie [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md) que notifiquem automaticamente os participantes do gasto de anomalias e degastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. No entanto, orçamentos e alertas podem ter funcionalidade limitada para gerenciar os custos de serviço do Azure individuais, pois eles foram projetados para controlar os custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar recursos com Azure Machine Learning, você incorre em custos. Os custos de unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade de solicitação. Assim que o uso de Azure Machine Learning é iniciado, os custos são incorridos. Exiba esses custos no painel [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md) na portal do Azure.

Você pode exibir os custos em gráficos e tabelas para intervalos de tempo diferentes. Você também pode exibir os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo ajuda a identificar tendências de gastos e a ver onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, confira onde eles foram excedidos.  

Você não verá uma área de serviço separada para Machine Learning.  Em vez disso, você verá os vários recursos que adicionou aos seus espaços de trabalho do Machine Learning. 

Observe que, embora o espaço de trabalho Machine Learning em si não tenha custo direto, você será cobrado em recursos de computação gerenciados. Portanto, se você adicionou marcas aos espaços de trabalho, já que não há nenhum custo direto, o painel de análise de custo refletirá isso para o espaço de trabalho. Para um gerenciamento de custo preciso por meio de marcas, você precisará marcar os recursos de computação associados.  

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Usar Azure Machine Learning cluster de computação (AmlCompute)

Com dados em constante mudança, você precisa de treinamento de modelo rápido e simplificado e retreinamento para manter modelos precisos. No entanto, o treinamento contínuo vem a um custo, especialmente para modelos de aprendizado profundo em GPUs. 

Azure Machine Learning usuários podem usar o cluster de computação de Azure Machine Learning gerenciado, também chamado de AmlCompute. O AmlCompute dá suporte a uma variedade de opções de GPU e CPU. O AmlCompute é hospedado internamente em nome de sua assinatura por Azure Machine Learning. Ele fornece a mesma segurança, conformidade e governança de nível empresarial na escala de nuvem IaaS do Azure.

Como esses pools de computação estão dentro da infraestrutura IaaS do Azure, você pode implantar, dimensionar e gerenciar seu treinamento com os mesmos requisitos de segurança e conformidade que o restante da sua infraestrutura.  Essas implantações ocorrem em sua assinatura e obedecem às suas regras de governança. Saiba mais sobre [Azure Machine Learning computação](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Configurar clusters de treinamento para dimensionamento automático

O dimensionamento automático de clusters com base nos requisitos de sua carga de trabalho ajuda a reduzir os custos para que você só use o que precisa.

Os clusters AmlCompute são projetados para dimensionar dinamicamente com base em sua carga de trabalho. O cluster pode ser dimensionado até o número máximo de nós que você configurar. À medida que cada execução for concluída, o cluster lançará os nós e dimensionará para a contagem de nós mínima configurada.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Você também pode configurar a quantidade de tempo que o nó está ocioso antes de reduzir verticalmente. Por padrão, o tempo ocioso antes da redução vertical é definido como 120 segundos.

+ Se você executar uma experimentação com menos iteração, reduza esse tempo para economizar custos.
+ Se você executar experimentação de desenvolvimento/teste altamente iterativo, talvez seja necessário aumentar o tempo para que você não esteja pagando pelo dimensionamento constante para cima e para baixo após cada alteração no seu ambiente ou script de treinamento.

Os clusters AmlCompute podem ser configurados para seus requisitos de carga de trabalho em alteração no portal do Azure, usando a [classe AMLCOMPUTE SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py), [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute), com as [APIs REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Definir cotas em recursos

O AmlCompute vem com uma [configuração de cota (ou limite)](how-to-manage-quotas.md#azure-machine-learning-compute). Essa cota é por família de VMs (por exemplo, série Dv2, série NCv3) e varia por região para cada assinatura. As assinaturas começam com pequenos padrões para ajudá-lo, mas use essa configuração para controlar a quantidade de recursos Amlcompute disponíveis para ser girada em sua assinatura. 

Também configure a [cota de nível de espaço de trabalho por família de VM](how-to-manage-quotas.md#workspace-level-quotas)para cada espaço de trabalho em uma assinatura. Isso permite que você tenha um controle mais granular dos custos que cada espaço de trabalho pode potencialmente incorrer e restringir determinadas famílias de VMs. 

Para definir cotas no nível do espaço de trabalho, inicie na [portal do Azure](https://portal.azure.com).  Selecione qualquer espaço de trabalho em sua assinatura e selecione **usos + cotas** no painel esquerdo. Em seguida, selecione a guia **configurar cotas** para exibir as cotas. Você precisa de privilégios no escopo da assinatura para definir a cota, pois é uma configuração que afeta vários espaços de trabalho.

## <a name="set-run-autotermination-policies"></a>Definir as políticas de execução de autoterminação 

Em alguns casos, você deve configurar as execuções de treinamento para limitar sua duração ou encerrá-las no início. Por exemplo, quando você estiver usando o ajuste de hiperparâmetro interno do Azure Machine Learning ou o aprendizado de máquina automatizado.

Aqui estão algumas opções que você tem:
* Defina um parâmetro chamado `max_run_duration_seconds` em seu RunConfiguration para controlar a duração máxima que uma execução pode estender para na computação escolhida (computação em nuvem local ou remota).
* Para [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md#early-termination), defina uma política de encerramento inicial de uma política de Bandit, uma política de interrupção mediana ou uma política de seleção de truncamento. Para controlar ainda mais a varredura de hiperparâmetro, use parâmetros como `max_total_runs` ou `max_duration_minutes` .
* Para o [aprendizado de máquina automatizado](how-to-configure-auto-train.md#exit), defina políticas de encerramento semelhantes usando o  `enable_early_stopping` sinalizador. Use também propriedades como `iteration_timeout_minutes` e `experiment_timeout_minutes` para controlar a duração máxima de uma execução ou para todo o experimento.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Usar VMs de baixa prioridade

O Azure permite que você use o excesso de capacidade inutilizável que Low-Priority VMs entre conjuntos de dimensionamento de máquinas virtuais, lote e o serviço de Machine Learning. Essas alocações são emptible, mas vêm a um preço reduzido em comparação com as VMs dedicadas. Em geral, é recomendável usar VMs Low-Priority para cargas de trabalho do lote. Você também deve usá-los onde as interrupções são recuperáveis por meio de reenvios (para inferência de lote) ou por meio de reinicializações (para treinamento de aprendizado profundo com ponto de verificação).

Low-Priority VMs têm uma única cota separada do valor de cota dedicado, que é por família de VM. Saiba [mais sobre as cotas do AmlCompute](how-to-manage-quotas.md).

 Low-Priority VMs não funcionam para instâncias de computação, pois precisam dar suporte a experiências interativas de notebook.

## <a name="use-reserved-instances"></a>Usar instâncias reservadas

Outra maneira de economizar dinheiro em recursos de computação é a instância de VM reservada do Azure. Com essa oferta, você se compromete com os termos de um ou três anos. Esses descontos vão até 72% dos preços pagos conforme o uso e são aplicados diretamente à sua fatura mensal do Azure.

Azure Machine Learning computação dá suporte a instâncias reservadas inerentemente. Se você comprar uma instância reservada de um ano ou de três anos, iremos aplicar automaticamente o desconto em sua computação Azure Machine Learning gerenciada.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
* [Gerenciar e aumentar as cotas de recursos](how-to-manage-quotas.md)
* [Gerenciamento de custos com análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Crie Azure Machine Learning computação com o [SDK](how-to-create-attach-compute-cluster.md) ou no [Studio](how-to-create-attach-compute-studio.md#amlcompute).