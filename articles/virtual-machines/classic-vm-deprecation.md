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
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754796"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migre seus recursos de IaaS para Azure Resource Manager até 1º de março de 2023 

Em 2014, lançamos o IaaS em Azure Resource Manager e já estamos aprimorando os recursos desde o momento. Como [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) agora tem recursos completos de IaaS e outros avanços, preterimos o gerenciamento de VMs de IaaS por meio [do Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) em 28 de fevereiro de 2020 e essa funcionalidade será totalmente desativada em 1º de março de 2023. 

Hoje, cerca de 90% das VMs de IaaS estão usando Azure Resource Manager. Se você usar recursos de IaaS por meio do Service Manager do Azure (ASM), comece a planejar sua migração agora e conclua-a até 1º de março de 2023 para tirar proveito do [Azure Resource Manager](../azure-resource-manager/management/index.yml).

As VMs clássicas estarão seguindo a [política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para aposentadoria.

## <a name="how-does-this-affect-me"></a>Como isso me afeta? 

- A partir de 28 de fevereiro de 2020, os clientes que não utilizaram VMs IaaS por meio do Azure Service Manager (ASM) no mês de fevereiro de 2020 não poderão mais criar VMs clássicas. 
- Em 1º de março de 2023, os clientes não poderão mais iniciar as VMs de IaaS usando o Service Manager do Azure e qualquer uma que ainda esteja em execução ou alocada será interrompida e desalocada. 
- Em 1º de março de 2023, as assinaturas que não são migradas para Azure Resource Manager serão informadas sobre as linhas do tempo para excluir as VMs clássicas restantes.  

Os seguintes serviços e funcionalidades do Azure **não** serão afetados por esta aposentadoria: 
- Serviços de Nuvem 
- Contas de armazenamento **não** usadas por VMS clássicas 
- Redes virtuais (VNets) **não** são usadas por VMS clássicas. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Quais ações devo tomar? 

- Comece a planejar sua migração para Azure Resource Manager, hoje mesmo. 

- Faça uma lista de todas as VMs afetadas. O tipo de VM "máquinas virtuais (clássicas)" na [folha de VM do portal do Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) são todas as VMs afetadas na assinatura. 

- [Saiba mais](./windows/migration-classic-resource-manager-overview.md) sobre como migrar suas VMs clássicas do [Linux](./linux/migration-classic-resource-manager-plan.md) e do [Windows](./windows/migration-classic-resource-manager-plan.md) para Azure Resource Manager.

- Para obter mais informações, consulte as [perguntas frequentes sobre a migração clássica para Azure Resource Manager](./migration-classic-resource-manager-faq.md)

- Para questões técnicas, problemas e adicionar assinaturas à lista de permissões, [contate o suporte](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Para outras perguntas que não fazem parte das perguntas frequentes e comentários, comente abaixo.

- Conclua a migração o mais rápido possível para evitar o impacto nos negócios e aproveitar o desempenho aprimorado, segurança & novos recursos fornecidos pelo Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Quais recursos são fornecidos para mim para essa migração?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): suporte da Comunidade do Microsoft & para migração

- [Suporte à migração do Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): equipe de suporte dedicada para assistência técnica durante a migração

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): a equipe do Microsoft Fast Track pode fornecer assistência técnica durante a migração para clientes qualificados. 

- Se sua empresa/organização tiver feito uma parceria com a Microsoft e/ou trabalhar com um representante da Microsoft como o (CSA (Storage Solution Architect), o TAMs (gerente técnico de contas)), trabalhe com eles para obter recursos adicionais para a migração. 

