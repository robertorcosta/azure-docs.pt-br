---
title: incluir arquivo
description: incluir arquivo
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/26/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8ee5973afb9312688178abd9a186c5319032c493
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506031"
---
O uso de VMs pontuais permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e etc. Ao implantar VMs Spot, o Azure alocará as VMs se houver capacidade disponível, mas não haverá SLA para essas VMs. Uma VM Spot não oferece nenhuma garantia de alta disponibilidade. A qualquer momento quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuadas com um aviso de 30 segundos. 


## <a name="eviction-policy"></a>Política de remoção

As VMs podem ser removidas com base na capacidade ou no preço máximo definido. Ao criar uma VM Spot, você pode definir a política de remoção como *desalocar* (padrão) ou *excluir*. 

A política de *desalocação* move a VM para o estado parado e desalocada, permitindo que você a implante novamente mais tarde. No entanto, não há nenhuma garantia de que a alocação terá êxito. As VMs desalocadas serão contadas em relação à sua cota e você será cobrado pelos custos de armazenamento para os discos subjacentes. 

Se você quiser que sua VM seja excluída quando ela for removida, você poderá definir a política de remoção a *ser excluída.* As VMs removidas são excluídas junto com seus discos subjacentes, portanto, você não continuará a ser cobrado pelo armazenamento. 

> [!NOTE]
>
> No momento, o portal não oferece suporte `Delete` como uma opção de remoção. você só pode definir `Delete` usando o PowerShell, a CLI e os modelos.

Você pode optar por receber notificações na VM por meio [do Azure eventos agendados](../articles/virtual-machines/linux/scheduled-events.md). Isso notificará você se suas VMs estiverem sendo removidas e você terá 30 segundos para concluir todos os trabalhos e realizar tarefas de desligamento antes da remoção. 


| Opção | Resultado |
|--------|---------|
| O preço máximo é definido como >= o preço atual. | A VM será implantada se a capacidade e a cota estiverem disponíveis. |
| O preço máximo é definido para < o preço atual. | A VM não está implantada. Você receberá uma mensagem de erro informando que o preço máximo precisa ser >= preço atual. |
| Reiniciar uma VM de parar/desalocar se o preço máximo for >= o preço atual | Se houver capacidade e cota, a VM será implantada. |
| Reiniciar uma VM de parar/desalocar se o preço máximo for < o preço atual | Você receberá uma mensagem de erro informando que o preço máximo precisa ser >= preço atual. | 
| O preço da VM foi concluído e agora está > o preço máximo. | A VM é removida. Você Obtém uma notificação de 30s antes da remoção real. | 
| Após a remoção, o preço da VM volta a ser < o preço máximo. | A VM não será reiniciada automaticamente. Você pode reiniciar a VM por conta própria e ela será cobrada com o preço atual. |
| Se o preço máximo for definido como`-1` | A VM não será removida por motivos de preço. O preço máximo será o preço atual, até o preço das VMs padrão. Você nunca será cobrado acima do preço padrão.| 
| Alterando o preço máximo | Você precisa desalocar a VM para alterar o preço máximo. Desaloque a VM, defina um novo preço máximo e, em seguida, atualize a VM. |


## <a name="limitations"></a>Limitações

Os seguintes tamanhos de VM não têm suporte para VMs spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como Dv2, NV, NC, tamanhos promocionais de H)

As VMs pontuais podem ser implantadas em qualquer região, exceto Microsoft Azure a 21Vianet da China.

Não há suporte para alguns canais de assinatura:

<a name="channel"></a>

| Canais do Azure               | Disponibilidade de VMs do Azure Spot       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pré-pago                | Sim                               |
| Provedor de Serviços de Nuvem (CSP) | [Entre em contato com seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Sim                               |
| Avaliação gratuita                   | Não disponível                     |



## <a name="pricing"></a>Preços

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Como o preço é variável, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0,98765 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.


##  <a name="frequently-asked-questions"></a>Perguntas frequentes

**P:** Depois de criada, é uma VM Spot a mesma que a VM normal padrão?

**R:** Sim, exceto que não há SLA para VMs do Spot e elas podem ser removidas a qualquer momento.


**P:** O que fazer quando você é removido, mas ainda precisa de capacidade?

**R:** Recomendamos que você use VMs padrão em vez de VMs do Spot se precisar de capacidade imediatamente.


**P:** Como a cota é gerenciada para VMs pontuais?

**R:** As VMs pontuais terão um pool de cotas separado. A cota do Spot será compartilhada entre as VMs e as instâncias do conjunto de dimensionamento. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P:** Posso solicitar uma cota adicional para o Spot?

**R:** Sim, você poderá enviar a solicitação para aumentar sua cota de VMs do Spot por meio do [processo de solicitação de cota padrão](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**P:** Onde posso postar perguntas?

**R:** Você pode postar e marcar sua pergunta com `azure-spot` em [Perguntas e respostas](https://docs.microsoft.com/answers/topics/azure-spot.html). 



