---
title: Estamos aposentando VMs do Azure (clássico) em 1º de março de 2023
description: Este artigo fornece uma visão geral de alto nível da desativação das VMs criadas usando o modelo de implantação clássico.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7f2db507176d65e7794607e83db8605b2f892c1c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646639"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migre seus recursos de IaaS para Azure Resource Manager até 1º de março de 2023 

Em 2014, lançamos a infraestrutura como um serviço (IaaS) em [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Já estamos aprimorando os recursos desde então. Como Azure Resource Manager agora tem recursos completos de IaaS e outros avanços, preterimos o gerenciamento de VMs (máquinas virtuais) IaaS por meio [do Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) em 28 de fevereiro de 2020. Essa funcionalidade será totalmente desativada em 1º de março de 2023. 

Hoje, cerca de 90% das VMs de IaaS estão usando Azure Resource Manager. Se você usar recursos de IaaS por meio do ASM, comece a planejar sua migração agora mesmo. Conclua-o até 1º de março de 2023 para aproveitar a [Azure Resource Manager](../azure-resource-manager/management/index.yml).

As VMs criadas usando o modelo de implantação clássico seguirão a [política moderna do ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para desativação.

## <a name="how-does-this-affect-me"></a>Como isso me afeta? 

- A partir de 28 de fevereiro de 2020, os clientes que não utilizaram VMs IaaS por meio do ASM no mês de fevereiro de 2020 não poderão mais criar VMs (clássicas). 
- Em 1º de março de 2023, os clientes não poderão mais iniciar as VMs de IaaS usando o ASM. Todos os que ainda estiverem em execução ou alocados serão interrompidos e desalocados. 
- Em 1º de março de 2023, as assinaturas que não são migradas para Azure Resource Manager serão informadas sobre as linhas do tempo para excluir as VMs restantes (clássicas).  

Essa aposentadoria não *afeta os* seguintes serviços e funcionalidades do Azure: 
- Serviços de nuvem do Azure 
- Contas de armazenamento *não* usadas por VMS (clássicas) 
- Redes virtuais *não* usadas por VMS (clássicas) 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Quais ações devo tomar? 

Comece a planejar sua migração para Azure Resource Manager, hoje mesmo. 

1. Faça uma lista de todas as VMs afetadas: 

   - As VMs do tipo **máquinas virtuais (clássicas)** no [painel VM do portal do Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) são todas as VMs afetadas na assinatura. 
   - Você também pode consultar o grafo de recursos do Azure usando o [portal](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) ou o [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) para exibir a lista de todas as VMs sinalizadas (clássicas) e informações relacionadas para as assinaturas selecionadas. 
   - Em 8 de fevereiro e 2 de setembro de 2020, enviamos emails para proprietários de assinatura com uma lista de todas as assinaturas que contêm essas VMs (clássicas). Use-os para criar essa lista. 

1. [Saiba mais](./windows/migration-classic-resource-manager-overview.md) sobre como migrar suas VMs [Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) (clássicas) para Azure Resource Manager. Para obter mais informações, consulte perguntas frequentes [sobre a migração clássica para Azure Resource Manager](./migration-classic-resource-manager-faq.md).

1. É recomendável iniciar o planejamento usando a [ferramenta de migração de suporte à plataforma](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) para migrar suas VMs existentes com três etapas simples: validar, preparar e confirmar. A ferramenta foi projetada para migrar suas VMs em um mínimo ou sem tempo de inatividade. 

   1. A primeira etapa, validar, não tem impacto sobre a implantação existente e fornece uma lista de todos os cenários sem suporte para migração. 
   1. Percorra a [lista de soluções alternativas](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) para corrigir sua implantação e torná-la pronta para a migração. 
   1. O ideal é que todos os erros de validação sejam corrigidos, você não deve encontrar nenhum problema durante as etapas de preparação e confirmação. Depois que a confirmação for bem-sucedida, sua implantação será migrada ao vivo para Azure Resource Manager e poderá ser gerenciada por meio de novas APIs expostas por Azure Resource Manager. 

   Se a ferramenta de migração não for adequada para sua migração, você poderá explorar [outras ofertas de computação](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) para a migração. Como há muitas ofertas de computação do Azure e elas são diferentes uma da outra, não podemos fornecer um caminho de migração com suporte da plataforma para elas.  

1. Para questões técnicas, problemas e ajuda com a adição de assinaturas à lista de permissões, [contate o suporte](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Conclua a migração o mais rápido possível para evitar o impacto nos negócios e aproveitar o desempenho, a segurança e os novos recursos aprimorados do Azure Resource Manager. 

## <a name="what-resources-are-available-for-this-migration"></a>Quais recursos estão disponíveis para essa migração?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): Microsoft e suporte da Comunidade para migração.

- [Suporte à migração do Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): equipe de suporte dedicada para assistência técnica durante a migração.

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): equipe que pode fornecer assistência técnica durante a migração para clientes qualificados. 

Se sua empresa/organização tiver parceria com a Microsoft ou trabalha com representantes da Microsoft (como arquitetos de soluções de nuvem (CSAs) ou gerentes de contas técnicas (TAMs)), trabalhe com eles para obter recursos adicionais para a migração. 

