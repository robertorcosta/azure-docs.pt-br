---
title: Estamos desativando as VMs clássicas do Azure em 1º de março de 2023
description: O artigo fornece uma visão geral de alto nível da desativação da VM clássica
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 6ddc9299f72e8a0b25e8b1f85968933bd6868af8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288541"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migre seus recursos de IaaS para Azure Resource Manager até 1º de março de 2023 

Em 2014, lançamos o IaaS em Azure Resource Manager e já estamos aprimorando os recursos desde o momento. Como [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) agora tem recursos completos de IaaS e outros avanços, preterimos o gerenciamento de VMs de IaaS por meio do Azure Service Manager em 28 de fevereiro de 2020 e essa funcionalidade será totalmente desativada em 1º de março de 2023. 

Hoje, cerca de 90% das VMs de IaaS estão usando Azure Resource Manager. Se você usar recursos de IaaS por meio do Service Manager do Azure (ASM), comece a planejar sua migração agora e conclua-a até 1º de março de 2023 para tirar proveito do [Azure Resource Manager](../azure-resource-manager/management/index.yml).

As VMs clássicas estarão seguindo a [política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para aposentadoria.

## <a name="how-does-this-affect-me"></a>Como isso me afeta? 

1) A partir de 28 de fevereiro de 2020, os clientes que não utilizaram VMs IaaS por meio do Azure Service Manager (ASM) no mês de fevereiro de 2020 não poderão mais criar VMs clássicas. 
2) Em 1º de março de 2023, os clientes não poderão mais iniciar as VMs de IaaS usando o Service Manager do Azure e qualquer uma que ainda esteja em execução ou alocada será interrompida e desalocada. 
2) Em 1º de março de 2023, as assinaturas que não foram migradas para Azure Resource Manager serão informadas sobre as linhas do tempo para excluir as VMs clássicas restantes.  

Os seguintes serviços e funcionalidades do Azure **não** serão afetados por esta aposentadoria: 
- Serviços de Nuvem 
- Contas de armazenamento **não** usadas por VMS clássicas 
- Redes virtuais (VNets) **não** são usadas por VMS clássicas. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Quais ações devo tomar? 

- Comece a planejar sua migração para Azure Resource Manager, hoje mesmo. 

- [Saiba mais](./windows/migration-classic-resource-manager-overview.md) sobre como migrar suas VMs clássicas do [Linux](./linux/migration-classic-resource-manager-plan.md) e do [Windows](./windows/migration-classic-resource-manager-plan.md) para Azure Resource Manager.

- Para obter mais informações, consulte as [perguntas frequentes sobre a migração clássica para Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md)

- Para questões técnicas, problemas e adicionar assinaturas à lista de permissões, [contate o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Para outras perguntas que não fazem parte das perguntas frequentes e comentários, comente abaixo.
