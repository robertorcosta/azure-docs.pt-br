---
title: Gerenciar recursos e cotas
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as cotas para recursos para o Azure Machine Learning e como solicitar mais cotas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 93cf29651110356023752f2377c9a0fc358982a7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203034"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gerenciar e aumentar cotas para recursos com o Azure Machine Learning

O Azure usa limites e cotas para evitar as execuções de orçamento por causa de fraudes e para honrar as restrições de capacidade do Azure. Considere esses limites conforme você dimensiona para cargas de trabalho de produção. Neste artigo, você aprenderá sobre:

> [!div class="checklist"]
> + Limites padrão de recursos do Azure relacionados a [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Exiba suas cotas e limites.
> + Crie cotas no nível do espaço de trabalho.
> + A cota de solicitação aumenta.
> + O ponto de extremidade privado e as cotas de DNS.

Juntamente com o gerenciamento de cotas, você também pode aprender a [planejar & gerenciar os custos de Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se você tiver necessidades de capacidade em grande escala, [entre em contato com o suporte do Azure para aumentar sua cota](#request-quota-increases).

+ A cota é compartilhada entre todos os serviços em suas assinaturas, incluindo Azure Machine Learning. Calcule o uso em todos os serviços ao avaliar a capacidade.
    + Azure Machine Learning computação é uma exceção e tem uma cota separada da cota de computação principal. 

+ Os limites padrão variam por tipo de categoria de oferta, como avaliação gratuita, pré-pago e série de máquinas virtuais (VM), como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Cotas de recurso padrão

Nesta seção, você aprenderá sobre os limites de cota padrão e máximo para os seguintes recursos:

+ Máquinas virtuais
+ Computação do Azure Machine Learning
+ Pipelines de Azure Machine Learning
+ Instâncias de Contêiner
+ Armazenamento

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrada no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) da cota de nível de serviço para tudo que é do Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Cada assinatura do Azure tem um limite no número de máquinas virtuais em todos os serviços. Os núcleos de máquina virtual têm um limite regional total e um limite regional por série de tamanho (Dv2, F, etc.). Ambos os limites são aplicados separadamente.

Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Essa assinatura teria permissão para implantar 30 VMs a1 ou 30 VMs D1 ou uma combinação dos dois que não exceda um total de 30 núcleos.

Os limites para máquinas virtuais não podem ser gerados acima do valor mostrado na tabela a seguir.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
[Azure Machine Learning computação](concept-compute-target.md#azure-machine-learning-compute-managed) tem um limite de cota padrão no número de núcleos e no número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota de núcleos de VM da seção anterior.

[Solicite um aumento de cota](#request-quota-increases) para elevar os limites desta seção até o **limite máximo** mostrado na tabela.

Recursos disponíveis:
+ Os **núcleos dedicados por região** têm um limite padrão de 24-300, dependendo do tipo de oferta de sua assinatura.  O número de núcleos dedicados por assinatura pode ser aumentado para cada família de VMs. Famílias de VMs especializadas como NCv2, NCv3 ou ND Series começam com um padrão de zero núcleos.

+ Os **núcleos de baixa prioridade por região** têm um limite padrão de 100-3000, dependendo do tipo de oferta da sua assinatura. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um valor único nas famílias de VM.

+ **Clusters por região** têm um limite padrão de 200. Eles são compartilhados entre um cluster de treinamento e uma instância de computação (que é considerada como um cluster de nó único para fins de cota).

A tabela a seguir mostra limites adicionais que não podem ser excedidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Espaços de trabalho por grupo de recursos | 800 |
| Nós em um único recurso de computação de Azure Machine Learning (AmlCompute) | 100 nós |
| Processos MPI de GPU por nó | 1-4 |
| Trabalhos de GPU por nó | 1-4 |
| Tempo de vida do trabalho | 21 dias<sup>1</sup> |
| Tempo de vida do trabalho em um nó de Low-Priority | 7 dias<sup>2</sup> |
| Servidores de parâmetro por nó | 1 |

<sup>1</sup> o tempo de vida máximo refere-se à duração entre quando uma execução é iniciada e concluída. As execuções concluídas persistirão indefinidamente. Os dados para execuções não concluídas no tempo de vida máximo não estão acessíveis.
<sup>2</sup> Trabalhos em um nó de baixa prioridade poderão ser admitidos sempre que houver uma restrição de capacidade. Recomendamos que você implemente pontos de verificação em seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Pipelines do Azure Machine Learning
[Azure Machine Learning pipelines](concept-ml-pipelines.md) têm os limites a seguir.

| **Recurso** | **Limite** |
| --- | --- |
| Etapas em um pipeline | 30,000 |
| Espaços de trabalho por grupo de recursos | 800 |

### <a name="container-instances"></a>Instâncias de contêiner

Para obter mais informações, consulte [limites de instâncias de contêiner](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
As contas de armazenamento do Azure têm um limite de 250 contas de armazenamento por região, por assinatura. Isso inclui contas de armazenamento Standard e Premium.

Para aumentar o limite, faça uma solicitação por meio [do suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de armazenamento do Azure examinará seu caso e poderá aprovar até 250 contas de armazenamento para uma região.


## <a name="workspace-level-quota"></a>Cota de nível de workspace

Use cotas de nível de espaço de trabalho para gerenciar Azure Machine Learning a alocação de destino de computação entre vários [espaços de trabalho](concept-workspace.md) na mesma assinatura.

Por padrão, todos os espaços de trabalho compartilham a mesma cota que a cota de nível de assinatura para as famílias de VMs. No entanto, você pode definir uma cota máxima para famílias de VM individuais em espaços de trabalho em uma assinatura. Isso permite que você compartilhe a capacidade e evite problemas de contenção de recursos:

1. Navegue até qualquer espaço de trabalho em sua assinatura.
1. No painel esquerdo, selecione **usos + cotas**.
1. Selecione a guia **configurar cotas** para exibir as cotas.
1. Expanda uma família de VMs.
1. Defina um limite de cota em qualquer espaço de trabalho listado nessa família de VMs.

Você não pode definir um valor negativo ou um valor maior que a cota de nível de assinatura.

[![Cota de nível de workspace do Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Você precisa de permissões de nível de assinatura para definir a cota no nível do workspace.

## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Para exibir sua cota para vários recursos do Azure, como máquinas virtuais, armazenamento, rede, use o portal do Azure:

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Assinaturas** na categoria Geral.

2. Na lista de assinaturas, selecione a assinatura cuja cota você está procurando.

3. Selecione **uso + cotas** para exibir os limites de cota e o uso atuais. Use os filtros para selecionar o provedor e os locais. 

A cota de computação Azure Machine Learning em sua assinatura é gerenciada separadamente de outras cotas do Azure. 

1. Navegue até o espaço de trabalho do **Azure Machine Learning** na portal do Azure.

2. No painel esquerdo, na **seção suporte + solução de problemas** , selecione **uso + cotas** para exibir os limites e o uso da cota atual.

3. Selecione uma assinatura para exibir os limites de cota. Lembre-se de filtrar para a região em que você está interessado.

4. Você pode alternar entre uma exibição de nível de assinatura e as exibições de nível de espaço de trabalho.

## <a name="request-quota-increases"></a>Solicitar aumentos de cota

Para aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) de atendimento ao cliente online sem encargos.

Os limites não podem ser gerados acima do valor de limite máximo mostrado nas tabelas acima. Se não houver um limite máximo, você não poderá ajustar o limite para o recurso.

Ao solicitar um aumento de cota, selecione o serviço ao qual você está solicitando a cota. Por exemplo Azure Machine Learning, instâncias de contêiner, armazenamento, etc. Para Azure Machine Learning computação, você pode selecionar o botão **solicitar cota** ao exibir a cota seguindo as etapas acima.

> [!NOTE]
> As [assinaturas de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para os aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualizar a Versão de Avaliação Gratuita do Azure para Pagamento Conforme o Uso](../billing/billing-upgrade-azure-subscription.md) e [Perguntas Frequentes sobre a assinatura de Avaliação Gratuita](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumento de cota de ponto de extremidade particular e de DNS privado

Há limitações no número de pontos de extremidade privados e zonas DNS privadas que podem ser criadas em uma assinatura.

Embora Azure Machine Learning crie recursos em sua assinatura do (cliente), há alguns cenários que criam recursos em uma assinatura de propriedade da Microsoft.

 Nos cenários a seguir, talvez seja necessário solicitar uma concessão de cota na assinatura da Microsoft:

* __Espaço de trabalho habilitado para link privado com uma chave gerenciada pelo cliente (CMK)__
* __Registro de contêiner do Azure para o espaço de trabalho por trás de sua rede virtual__
* __Anexando um link privado do cluster do serviço kubernetes do Azure habilitado ao seu espaço de trabalho__.

Para solicitar uma concessão para esses cenários, use as seguintes etapas:

1. [Crie uma solicitação de suporte do Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) e selecione as seguintes opções na seção __noções básicas__ :

    | Campo | Seleção |
    | ----- | ----- |
    | Tipo de problema | Técnicos |
    | Serviço | Meus serviços. Selecione __Machine Learning__ na lista suspensa. |
    | Tipo de problema | Instalação, SDK e CLI do espaço de trabalho |
    | Subtipo de problema | Ponto de extremidade particular e solicitação de concessão de zona de DNS privado |

2. Na seção __detalhes__ , use o campo __Descrição__ para fornecer a região do Azure que você deseja usar e o cenário que você planeja usar. Se você precisar solicitar aumentos de cota para várias assinaturas, liste também as IDs de assinatura neste campo.

3. Selecione __criar__ para criar a solicitação.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Captura de tela de um ponto de extremidade privado e uma solicitação de aumento de cota de DNS particular":::

## <a name="next-steps"></a>Próximas etapas

+ [Planejar e gerenciar custos para o Azure Machine Learning](concept-plan-manage-cost.md)
