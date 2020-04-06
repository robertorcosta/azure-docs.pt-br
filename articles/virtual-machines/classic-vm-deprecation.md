---
title: Vamos aposentar as VMs Azure Classic em 1º de março de 2023
description: Artigo fornece uma visão geral de alto nível da aposentadoria clássica da VM
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668799"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migre seus recursos de IaaS para o Azure Resource Manager até 1º de março de 2023 

Em 2014, lançamos o IaaS no Azure Resource Manager, e estamos aprimorando recursos desde então. Como [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) agora tem recursos completos de IaaS e outros avanços, preterimos o gerenciamento de VMs IaaS através do Azure Service Manager em 28 de fevereiro de 2020 e essa funcionalidade será totalmente aposentada em 1º de março de 2023. 

Hoje, cerca de 90% das VMs IaaS estão usando o Azure Resource Manager. Se você usar os recursos do IaaS através do Asm (Azure Service Manager, gerente de serviços do Azure), comece a planejar sua migração agora e complete-a até 1º de março de 2023 para aproveitar [o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

VMs clássicos seguirão a [Política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para a aposentadoria.

## <a name="how-does-this-affect-me"></a>Como isso me afeta? 

1) A partir de 28 de fevereiro de 2020, os clientes que não utilizaram VMs IaaS através do Azure Service Manager (ASM) no mês de fevereiro de 2020 não poderão mais criar VMs clássicos. 
2) Em 1º de março de 2023, os clientes não poderão mais iniciar VMs IaaS usando o Azure Service Manager e qualquer um que ainda esteja em execução ou alocado será parado e desalocado. 
2) Em 1º de março de 2023, as assinaturas que não migraram para o Azure Resource Manager serão informadas sobre os prazos para excluir quaisquer VMs clássicas restantes.  

Os seguintes serviços e funcionalidades do Azure **NÃO** serão impactados por esta aposentadoria: 
- Serviços de Nuvem 
- Contas de armazenamento **não** usadas por VMs clássicos 
- Redes virtuais (VNets) **não** utilizadas por VMs clássicos. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Que ações devo tomar? 

- Comece a planejar sua migração para o Azure Resource Manager, hoje. 

- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) sobre como migrar suas VMs clássicas [do Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) para o Azure Resource Manager.

- Para obter mais informações, consulte as [perguntas frequentes sobre a migração clássica do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Para perguntas técnicas, problemas e [suporte de contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de lista de assinaturas.

- Para outras perguntas que não fazem parte das perguntas frequentes e do feedback, comente abaixo.
