---
title: Gerenciar recursos & cotas
titleSuffix: Azure Machine Learning
description: Conheça as cotas de recursos para O Zure Machine Learning e como solicitar mais cotas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399145"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerenciar e solicitar cotas para recursos do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece detalhes sobre limites pré-configurados nos recursos do Azure para sua assinatura. Também estão incluídas instruções sobre como solicitar melhorias de cotas para cada tipo de recurso. Esses limites são estabelecidos para evitar estouros de orçamento devido a fraudes e respeitar as restrições de capacidade do Azure.

Como acontece com outros serviços do Azure, há limites para certos recursos associados ao Azure Machine Learning. Esses limites variam de um limite no número de espaços de trabalho a limites na computação subjacente real que é usada para treinamento de modelo ou inferência/pontuação. 

À medida que você projeta e dimensiona seus recursos de Machine Learning do Azure para cargas de trabalho de produção, considere esses limites. Por exemplo, se o cluster não atingir o número de nós-alvo, então você pode ter atingido um limite de núcleos de aprendizado de máquina do Azure para sua assinatura. Se você desejar aumentar o limite ou a cota acima do limite padrão, abra uma solicitação de suporte ao cliente online sem custo. Os limites não podem ser aumentados além do valor Limite Máximo mostrado nas tabelas a seguir devido a restrições de Capacidade do Azure. Se não houver nenhuma coluna Limite Máximo, o recurso não terá limites ajustáveis.

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se precisar de capacidade em larga escala, contate o suporte do Azure.

+ Sua cota é compartilhada em todos os serviços de suas assinaturas, incluindo o Azure Machine Learning. A única exceção é a Computação do Azure Machine Learning, que tem uma cota separada da cota de computação principal. Certifique-se de calcular o uso da cota em todos os serviços ao avaliar suas necessidades de capacidade.

+ Os limites padrão variam de acordo com o Tipo de Categoria da oferta, como séries de avaliação gratuita, pay-as-you-go e VM, como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Cotas de recurso padrão

Veja um detalhamento dos limites de cota por vários tipos de recurso dentro de sua assinatura do Azure.

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrada no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) da cota de nível de serviço para tudo que é do Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Para cada assinatura do Azure, há um limite no número de máquinas virtuais que você pode ter em seus serviços ou autônomos. Esse limite está no nível da região nos núcleos totais e também é calculado por família.

Os núcleos de máquinas virtuais têm um limite total regional e um limite regional por série de tamanho (Dv2, F, etc.), ambos são aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura teria permissão para implantar 30 VMs A1, ou 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, confira o site de cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para a Computação do Azure Machine Learning, há um limite de cota padrão para o número de núcleos e o número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota principal vm acima e os limites principais não são compartilhados entre os dois tipos de recursos, uma vez que o AmlCompute é um serviço gerenciado que implanta recursos em um modelo hospedado em nome.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24 a 300, dependendo do tipo de oferta de assinatura com padrões mais altos para tipos de ofertas EA e CSP.  O número de núcleos dedicados por assinatura pode ser aumentado e é diferente para cada família VM. Certas famílias de VM especializadas como ncv2, NCv3 ou nd séries começam com um padrão de núcleos zero. Entre em contato com o suporte do Azure levantando um pedido de cota para discutir opções de aumento.

+ Núcleos de baixa prioridade por região têm um limite padrão de 100 a 3000, dependendo do tipo de oferta de assinatura com padrões mais altos para tipos de oferta saem e CSP. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um valor único entre as famílias de VM. Entre em contato com o suporte do Azure para discutir opções de aumento.

+ Os clusters por região têm um limite padrão de 200. Estes são compartilhados entre um cluster de treinamento e uma instância de computação (que é considerado como um único cluster de nós para fins de cota). Contate o suporte do Azure se desejar solicitar um aumento além desse limite.

+ Existem **outros limites rígidos que não podem ser excedidos uma vez atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Máximo de workspaces por grupo de recursos | 800 |
| Máximo de nós em um único recurso de Computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de processos de MPI de GPU nó | 1-4 |
| Máximo de trabalhos de GPU nó | 1-4 |
| Tempo de vida máximo do trabalho | 90 dias<sup>1</sup> |
| Vida útil máxima em um nó de baixa prioridade | 7 dias<sup>2</sup> |
| Máximo de servidores de parâmetro por nó | 1 |

<sup>1</sup> O tempo de vida máximo se refere ao tempo desde que a execução começa até que seja concluída. Execuções concluídas persistem indefinidamente; dados de execuções não concluídas dentro do tempo de vida máximo não ficam acessíveis.
<sup>2</sup> Os trabalhos em um nó de baixa prioridade podem ser antecipados sempre que houver uma restrição de capacidade. Recomendamos que você implemente o checkpoint em seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Pipelines do Azure Machine Learning
Para o Azure Machine Learning Pipelines, há um limite de cotas sobre o número de etapas em um pipeline e sobre o número de corridas baseadas em cronograma de pipelines publicados por região em uma assinatura.
- O número máximo de etapas permitidas em um gasoduto é de 30.000
- O número máximo da soma das corridas baseadas em horários e puxa-bodes para horários desencadeados por blogs de pipelines publicados por assinatura por mês é de 100.000

> [!NOTE]
> Se você quiser aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instâncias de contêiner

Também há um limite no número de instâncias de contêiner que você pode criar em um determinado período (por hora com escopo) ou em sua assinatura inteira.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, confira o site de cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Há um limite no número de contas de armazenamento por região também em uma determinada assinatura. O limite padrão é de 250 e inclui contas de armazenamento padrão e premium. Se você precisar de mais de 250 contas de armazenamento em uma determinada região, faça uma solicitação através [do Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de Armazenamento do Microsoft Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento.


## <a name="workspace-level-quota"></a>Cota de nível de espaço de trabalho

Para gerenciar melhor as alocações de recursos para Amlcompute entre vários espaços de trabalho, introduzimos um recurso que permite distribuir cotas de nível de assinatura (pela família VM) e configurá-las no nível do espaço de trabalho. O comportamento padrão é que todos os espaços de trabalho têm a mesma cota que a cota de nível de assinatura para qualquer família De VM. No entanto, à medida que o número de espaços de trabalho aumenta e cargas de trabalho de prioridades variadas começam a compartilhar os mesmos recursos, os usuários querem uma maneira de compartilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com sua oferta de computação gerenciada, permitindo que os usuários definam uma cota máxima para uma família de VM específica em cada espaço de trabalho. Isso é análogo à distribuição da sua capacidade entre os espaços de trabalho, e os usuários podem optar por também sobrealocar para impulsionar a utilização máxima. 

Para definir cotas no nível de espaço de trabalho, vá para qualquer espaço de trabalho em sua assinatura e clique em **Usos + cotas** no painel esquerdo. Em seguida, **selecione** a guia Configurar cotas para visualizar as cotas, expandir qualquer família De VM e definir um limite de cotas em qualquer espaço de trabalho listado nessa família VM. Lembre-se de que você não pode definir um valor negativo ou um valor superior à cota de nível de assinatura. Além disso, como você observa, por padrão todos os espaços de trabalho são atribuídos toda a cota de assinatura para permitir a utilização integral da cota alocada.

[![Cota de nível de espaço de trabalho de Aprendizado de Máquina do Azure](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Este é apenas um recurso de edição Enterprise. Se você tiver um espaço de trabalho básico e uma edição Enterprise em sua assinatura, você pode usá-lo apenas para definir cotas em seus espaços de trabalho Enterprise. Seus espaços de trabalho básicos continuarão a ter a cota de nível de assinatura, que é o comportamento padrão.
>
> Você precisa de permissões de nível de assinatura para definir a cota no nível do espaço de trabalho. Isso é aplicado para que os proprietários individuais de espaços de trabalho não editem ou aumentem suas cotas e comecem a invadir recursos reservados para outro espaço de trabalho. Assim, um admin de assinatura é mais adequado para alocar e distribuir essas cotas em espaços de trabalho.



## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Por meio do portal do Azure, é fácil exibir sua cota para vários recursos, como Máquinas Virtuais, Armazenamento e Rede.

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Assinaturas** na categoria Geral.

1. Na lista de assinaturas, selecione a assinatura cuja cota você está procurando.

   **Há uma limitação**, especificamente para exibir a cota de Computação do Azure Machine Learning. Conforme mencionado acima, essa cota é separada da cota de computação em sua assinatura.

1. No painel esquerdo, selecione serviço **de Aprendizado de Máquina** e selecione qualquer espaço de trabalho da lista mostrada

1. Na folha seguinte, na seção **Suporte + solução de problemas**, selecione **Uso + cotas** para exibir os limites de cota e o uso atual.

1. Selecione uma assinatura para exibir os limites de cota. Lembre-se de filtrar para a região em que você está interessado.

1. Agora você pode alternar entre uma exibição de nível de assinatura e uma exibição de nível de espaço de trabalho:
    + **Exibição de assinatura:** Isso permite que você visualize seu uso da cota principal pela família VM, expandindo-a por espaço de trabalho e expandindo-a ainda mais pelos nomes de cluster reais. Essa visão é ideal para entrar rapidamente nos detalhes do uso do núcleo para uma determinada família de VM para ver o rompimento por espaços de trabalho e ainda mais pelos clusters subjacentes para cada um desses espaços de trabalho. A convenção geral nesta visão é (uso/cota), onde o uso é o número atual de núcleos escalonados, e a cota é o número máximo lógico de núcleos para o qual o recurso pode escalar. Para cada **espaço de trabalho,** a cota seria a cota de nível de espaço de trabalho (como explicado acima) que denota o número máximo de núcleos que você pode escalar para uma família De VM específica. Para um **cluster** da mesma forma, a cota é na verdade os núcleos correspondentes ao número máximo de nós que o cluster pode dimensionar para definido pela propriedade max_nodes.

    + **Exibição do espaço de trabalho:** Isso permite que você visualize seu uso da cota principal pelo Workspace, expandindo-a pela família VM e expandindo-a ainda mais pelos nomes de cluster reais. Essa visão é ideal para entrar rapidamente nos detalhes do uso do núcleo para um espaço de trabalho específico para ver o rompimento por famílias de VM e ainda mais pelos clusters subjacentes para cada uma dessas famílias.

## <a name="request-quota-increases"></a>Solicitar aumentos de cota

Se você quiser aumentar o limite ou cota acima do limite padrão, [abra uma solicitação de suporte ao cliente on-line](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) sem custo.

Os limites não podem ser aumentados acima do valor limite máximo mostrado nas tabelas. Se não houver limite máximo, então o recurso não terá limites ajustáveis. [Este](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo aborda o processo de aumento da cota em mais detalhes.

Ao solicitar um aumento da cota, é necessário selecionar o serviço com relação ao qual você está solicitando o aumento da cota, que poderiam ser serviços como a cota do serviço do Machine Learning, das instâncias de Contêiner ou da cota de Armazenamento. Além do Azure Machine Learning Compute, você pode clicar no botão **Cota de solicitação** enquanto visualiza a cota seguindo as etapas acima.

> [!NOTE]
> As [assinaturas de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para os aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualizar a Versão de Avaliação Gratuita do Azure para Pagamento Conforme o Uso](../billing/billing-upgrade-azure-subscription.md) e [Perguntas Frequentes sobre a assinatura de Avaliação Gratuita](https://azure.microsoft.com/free/free-account-faq).
