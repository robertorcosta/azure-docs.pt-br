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
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a25dcc187c1bb172106a3972c1cb57dfd473bc2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322369"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gerenciar e aumentar cotas para recursos com o Azure Machine Learning


Neste artigo, você aprenderá sobre os limites pré-configurados dos recursos do Azure para sua assinatura [Azure Machine Learning](overview-what-is-azure-ml.md) e quais cotas você pode gerenciar. Esses limites são estabelecidos para evitar estouros de orçamento devido a fraudes e respeitar as restrições de capacidade do Azure. 

Como com outros serviços do Azure, há limites em determinados recursos associados ao Azure Machine Learning. Esses limites variam de um limite no número de [workspaces](concept-workspace.md) que podem ser criados a limites na computação subjacente real que é usada para treinar ou inferir/pontuar modelos. 

Enquanto cria e aumenta seus recursos do Azure Machine Learning para cargas de trabalho de produção, considere esses limites. Por exemplo, se o cluster não atingir o número de destino de nós, você poderá ter atingido um limite de núcleos de Computação do Azure Machine Learning para sua assinatura. Se você desejar aumentar o limite ou a cota acima do limite padrão, abra uma solicitação de suporte ao cliente online sem custo. Os limites não podem ser aumentados além do valor Limite Máximo mostrado nas tabelas a seguir devido a restrições de Capacidade do Azure. Se não houver nenhuma coluna Limite Máximo, o recurso não terá limites ajustáveis.


Juntamente com o gerenciamento de cotas, você também pode aprender a [planejar & gerenciar os custos de Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se precisar de capacidade em larga escala, contate o suporte do Azure. Você também pode [aumentar suas cotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

+ A cota é compartilhada entre todos os serviços em suas assinaturas, incluindo o Azure Machine Learning. A única exceção é a Computação do Azure Machine Learning, que tem uma cota separada da cota de computação principal. Certifique-se de calcular o uso da cota em todos os serviços ao avaliar suas necessidades de capacidade.

+ Os limites padrão variam de acordo com o tipo de categoria oferecido, como avaliação gratuita, pagamento conforme o uso e séries de VM, como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Cotas de recurso padrão

Veja um detalhamento dos limites de cota por vários tipos de recurso dentro de sua assinatura do Azure.

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrada no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) da cota de nível de serviço para tudo que é do Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Para cada assinatura do Azure, há um limite no número de máquinas virtuais em seus serviços ou autônomos. Os núcleos de máquina virtual têm um limite regional total, bem como um limite regional por tamanho de série (Dv2, F etc.), aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura teria permissão para implantar 30 VMs A1, ou 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para a [Computação do Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed), há um limite de cota padrão para o número de núcleos e o número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota de núcleos de VM acima e os limites de núcleo não são compartilhados entre os dois tipos de recurso, uma vez que AmlCompute é um serviço gerenciado que implanta recursos em um modelo "hospedado em nome de".

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24 – 300, dependendo de seu tipo de oferta de assinatura, com padrões mais altos para tipos de oferta EA e CSP.  O número de núcleos dedicados por assinatura pode ser aumentado e é diferente para cada família de VM. Determinadas famílias de VM especializadas, como as séries NCv2, NCv3 ou ND, começam com um padrão de zero núcleo. Entre em contato com o Suporte do Azure fazendo uma solicitação de cota para discutir opções de aumento.

+ Os núcleos com baixa prioridade por região têm um limite padrão de 100 – 3.000, dependendo de seu tipo de oferta de assinatura, com padrões mais altos para tipos de oferta EA e CSP. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um valor único nas famílias de VM. Entre em contato com o suporte do Azure para discutir opções de aumento.

+ Os clusters por região têm um limite padrão de 200. Eles são compartilhados entre um cluster de treinamento e uma instância de computação (que é considerada como um cluster de nó único para fins de cota). Contate o suporte do Azure se desejar solicitar um aumento além desse limite.

+ Há outros limites rígidos que não podem ser ultrapassados uma vez que são atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Máximo de workspaces por grupo de recursos | 800 |
| Máximo de nós em um único recurso de Computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de processos de MPI de GPU nó | 1-4 |
| Máximo de trabalhos de GPU nó | 1-4 |
| Tempo de vida máximo do trabalho | 21 dias<sup>1</sup> |
| Tempo de vida máximo de trabalho em um nó de baixa prioridade | 7 dias<sup>2</sup> |
| Máximo de servidores de parâmetro por nó | 1 |

<sup>1</sup> O tempo de vida máximo se refere ao tempo desde que a execução começa até que seja concluída. Execuções concluídas persistem indefinidamente; dados de execuções não concluídas dentro do tempo de vida máximo não ficam acessíveis.
<sup>2</sup> Trabalhos em um nó de baixa prioridade poderão ser admitidos sempre que houver uma restrição de capacidade. Recomendamos que você implemente o ponto de verificação em seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Pipelines do Azure Machine Learning
Para [pipelines do Azure Machine Learning](concept-ml-pipelines.md), há um limite de cota no número de etapas em um pipeline e no número de execuções baseadas em agendamento de pipelines publicados por região em uma assinatura.
- O número máximo de etapas permitidas em um pipeline é de 30.000
- O número máximo da soma de execuções baseadas em agendamento e pulls de blob para agendamentos disparados por blog de pipelines publicados por assinatura por mês é de 100.000

### <a name="container-instances"></a>Instâncias de contêiner

Também há um limite no número de instâncias de contêiner que você pode criar em um determinado período (por hora com escopo) ou em sua assinatura inteira.
Para os limites, consulte [limites de instâncias de contêiner](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Há um limite no número de contas de armazenamento por região também em uma determinada assinatura. O limite padrão é 250 e inclui contas de Armazenamento Standard e Premium. Se você precisar de mais de 250 contas de armazenamento em uma única região, faça uma solicitação por meio do [Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de Armazenamento do Microsoft Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento.


## <a name="workspace-level-quota"></a>Cota de nível de workspace

Para gerenciar melhor as alocações de recursos para o destino da Computação do Azure Machine Learning (Amlcompute) entre vários [workspaces](concept-workspace.md), apresentamos um recurso que permite distribuir cotas de nível de assinatura (por família de VM) e configurá-las no nível do workspace. O comportamento padrão é que todos os workspaces têm a mesma cota que a cota de nível de assinatura para qualquer família de VM. No entanto, à medida que o número de workspaces aumenta e as cargas de trabalho de prioridade variável começam a compartilhar os mesmos recursos, os usuários desejam um modo de compartilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com sua oferta de computação gerenciada, permitindo que os usuários definam uma cota máxima para uma família de VM específica em cada workspace. Isso é análogo à distribuição de sua capacidade entre workspaces, e os usuários também podem optar por superalocar para aumentar a utilização máxima. 

Para definir cotas no nível do workspace, acesse qualquer workspace em sua assinatura e clique em **Usos + cotas** no painel esquerdo. Em seguida, selecione a guia **Configurar cotas** para exibir as cotas, expanda qualquer família de VMs e defina um limite de cota em um workspace listado nessa família de VMs. Lembre-se de que você não pode definir um valor negativo ou um valor maior que a cota de nível de assinatura. Além disso, como você observaria, por padrão, todos os workspaces recebem a cota de assinatura inteira para permitir a utilização total da cota alocada.

[![Cota de nível de workspace do Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Você precisa de permissões de nível de assinatura para definir a cota no nível do workspace. Isso é imposto para que os proprietários de workspaces individuais não editem ou aumentem suas cotas e comecem a interferir em recursos separados para outro workspace. Portanto, um administrador de assinatura é quem tem mais capacidade para alocar e distribuir essas cotas entre workspaces.



## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Azure Machine Learning cota de computação em sua assinatura é gerenciada separadamente de outra cota de recursos do Azure. Para exibir essa cota, você precisa fazer busca detalhada nos serviços do Machine Learning.  

1. No painel esquerdo, selecione **serviço do Machine Learning** e, em seguida, selecione qualquer workspace na lista mostrada.

2. Na folha seguinte, na seção **Suporte + solução de problemas**, selecione **Uso + cotas** para exibir os limites de cota e o uso atual.

3. Selecione uma assinatura para exibir os limites de cota. Lembre-se de filtrar para a região em que você está interessado.

4. Agora você pode alternar entre uma exibição de nível de assinatura e uma exibição no nível do workspace:
    + **Exibição da assinatura:** Isso permite que você exiba o uso da cota de núcleos por família de VM, expandindo-a por workspace e expandindo-a ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso de núcleo de uma família de VMs específica para ver a divisão por workspaces e além dos clusters subjacentes para cada um desses workspaces. A convenção geral nessa exibição é (uso/cota), em que o uso é o número atual de núcleos escalados verticalmente e a cota é o número máximo lógico de núcleos para os quais o recurso pode ser escalado. Para cada **workspace**, a cota seria a cota de nível do workspace (conforme explicado acima) que denota o número máximo de núcleos que você pode escalar para uma família de VMs específica. Da mesma forma, para um **cluster**, a cota é, na verdade, os núcleos que correspondem ao número máximo de nós que o cluster pode escalar para ser definido pela propriedade max_nodes.
    
    + **Exibição do workspace:** Isso permite que você exiba o uso da cota de núcleo por workspace, expandindo-o por família de VM e expandindo-o ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso de núcleo de um workspace específico para ver a divisão por famílias de VMs e além dos clusters subjacentes para cada uma dessas famílias.

Por meio do portal do Azure, é fácil exibir sua cota para outros recursos do Azure, como Máquinas Virtuais, Armazenamento e Rede.

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Assinaturas** na categoria Geral.

2. Na lista de assinaturas, selecione a assinatura cuja cota você está procurando.

3. Selecione **uso + cotas** para exibir os limites de cota e o uso atuais. Use os filtros para selecionar o provedor e os locais. 

## <a name="request-quota-increases"></a>Solicitar aumentos de cota

Se você desejar aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação de suporte ao cliente online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) sem custo.

Os limites não podem ser aumentados acima do valor limite máximo mostrado nas tabelas. Se não houver limite máximo, então o recurso não terá limites ajustáveis. [Consulte as instruções passo a passo sobre como aumentar sua cota](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Ao solicitar um aumento da cota, é necessário selecionar o serviço com relação ao qual você está solicitando o aumento da cota, que poderiam ser serviços como a cota do serviço do Machine Learning, das instâncias de Contêiner ou da cota de Armazenamento. Além disso, para a Computação do Azure Machine Learning, você pode clicar no botão **Solicitar Cota** ao exibir a cota seguindo as etapas acima.

> [!NOTE]
> As [assinaturas de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para os aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualizar a Versão de Avaliação Gratuita do Azure para Pagamento Conforme o Uso](../billing/billing-upgrade-azure-subscription.md) e [Perguntas Frequentes sobre a assinatura de Avaliação Gratuita](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumento de cota de ponto de extremidade particular e de DNS privado

Há limitações no número de pontos de extremidade privados e zonas DNS privadas que podem ser criadas em uma assinatura. Embora Azure Machine Learning crie recursos em sua assinatura do (cliente), há alguns cenários que criam recursos em uma assinatura de propriedade da Microsoft. Nos cenários a seguir, talvez seja necessário solicitar uma concessão de cota na assinatura da Microsoft:

* __Espaço de trabalho habilitado para link privado com uma chave gerenciada pelo cliente (CMK)__
* __Registro de contêiner do Azure para o espaço de trabalho por trás de sua rede virtual__
* __Anexando um link privado do cluster do serviço kubernetes do Azure habilitado ao seu espaço de trabalho__.

Para solicitar uma concessão para esses cenários, use as seguintes etapas:

1. [Crie uma solicitação de suporte do Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) e selecione as seguintes opções na seção __noções básicas__ :

    | Campo | Seleção |
    | ----- | ----- |
    | Tipo de problema | Técnico |
    | Serviço | Meus serviços. Selecione __Machine Learning__ na lista suspensa. |
    | Tipo de problema | Instalação, SDK e CLI do espaço de trabalho |
    | Subtipo de problema | Problema ao provisionar ou gerenciar o workspace |

2. Na seção __detalhes__ , use o campo __Descrição__ para fornecer a região do Azure que você deseja usar e o cenário que você planeja usar. Se você precisar solicitar aumentos de cota para várias assinaturas, liste também as IDs de assinatura neste campo.

3. Use __criar__ para criar a solicitação.

## <a name="next-steps"></a>Próximas etapas

+ [Planejar e gerenciar custos para o Azure Machine Learning](concept-plan-manage-cost.md)
