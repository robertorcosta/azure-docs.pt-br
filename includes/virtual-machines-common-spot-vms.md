---
title: incluir arquivo
description: incluir arquivo
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421748"
---
O uso de VMs Spot permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

A quantidade de capacidade disponível pode variar de acordo com o tamanho, região, hora do dia e muito mais. Ao implantar VMs Spot, o Azure alocará as VMs se houver capacidade disponível, mas não há SLA para essas VMs. Uma VM Spot não oferece garantias de alta disponibilidade. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot com 30 segundos de antecedência. 


## <a name="eviction-policy"></a>Política de despejo

As VMs podem ser despejadas com base na capacidade ou no preço máximo que você definir. Para máquinas virtuais, a política de despejo é definida para *Delocar,* que move suas VMs despejadas para o estado desalocado parado, permitindo que você reimplante as VMs despejadas posteriormente. No entanto, a realocação de VMs spot dependerá da disponibilidade da capacidade spot. As VMs desalocadas contarão com a sua cota spot vCPU e você será cobrado pelos discos subjacentes. 

Os usuários podem optar por receber notificações in-VM através [do Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Isso irá notificá-lo se suas VMs estão sendo despejadas e você terá 30 segundos para terminar qualquer trabalho e executar tarefas de desligamento antes do despejo. 


| Opção | Resultado |
|--------|---------|
| O preço máximo está definido para >= o preço atual. | A VM é implantada se houver capacidade e cota disponíveis. |
| O preço máximo está definido para < o preço atual. | O VM não está implantado. Você receberá uma mensagem de erro de que o preço máximo precisa ser >= preço atual. |
| Reiniciar um VM stop/deallocate se o preço máximo for >= o preço atual | Se houver capacidade e cota, então a VM será implantada. |
| Reiniciar um VM stop/deallocate se o preço máximo estiver < preço atual | Você receberá uma mensagem de erro de que o preço máximo precisa ser >= preço atual. | 
| O preço da VM subiu e agora está > o preço máximo. | A VM é despejada. Você recebe uma notificação dos anos 30 antes do despejo real. | 
| Após o despejo o preço da VM volta a ser < o preço máximo. | A VM não será reiniciada automaticamente. Você mesmo pode reiniciar o VM, e ele será cobrado pelo preço atual. |
| Se o preço máximo estiver definido para`-1` | A VM não será despejada por razões de preços. O preço máximo será o preço atual, até o preço das VMs padrão. Você nunca será cobrado acima do preço padrão.| 
| Alterando o preço máximo | Você precisa desalocar o VM para alterar o preço máximo. Desaloque a VM, defina um novo preço máximo e atualize a VM. |

## <a name="limitations"></a>Limitações

Os seguintes tamanhos de VM não são suportados para VMs spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como dv2, NV, NC, tamanhos de promoção H)

As VMs spot não podem usar discos efêmeros do sistema operacional.

As VMs spot podem ser implantadas em qualquer região, exceto no Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Preços

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis.


##  <a name="frequently-asked-questions"></a>Perguntas frequentes

**Q:** Uma vez criado, uma VM Spot é a mesma da VM padrão regular?

**A:** Sim, exceto que não há SLA para VMs spot e eles podem ser despejados a qualquer momento.


**Q:** O que fazer quando for despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que você use VMs padrão em vez de VMs Spot se você precisar de capacidade imediatamente.


**Q:** Como a cota é gerenciada para VMs Spot?

**A:** As VMs spot terão um pool de cotas separado. A cota spot será compartilhada entre VMs e instâncias definidas em escala. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q:** Posso solicitar uma cota adicional para spot?

**A:** Sim, você poderá enviar a solicitação para aumentar sua cota de VMs Spot através do [processo padrão de solicitação de cotas.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**Q:** Quais canais suportam VMs spot?

**A:** Veja a tabela abaixo para disponibilidade spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs do Azure Spot       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pago Conforme o Uso                | Sim                               |
| Provedor de Serviços de Nuvem (CSP) | [Entre em contato com seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Contrato de Cliente da Microsoft | Sim                               |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Não disponível                     |
| Avaliação gratuita                   | Não disponível                     |


**Q:** Onde posso postar perguntas?

**A:** Você pode postar e `azure-spot` marcar sua pergunta com [em Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



