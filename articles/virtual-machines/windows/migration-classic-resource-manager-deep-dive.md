---
title: Detalhes técnicos da migração clássica para Azure Resource Manager
description: Aprofundamento técnico sobre a migração de recursos com suporte da plataforma do modelo de implantação clássico para Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 5ef2085f41cca71e835e05bce97f787ad50a7a53
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508501"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs de IaaS estão usando [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente desativadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa reprovação e [como ela afeta você](../classic-vm-deprecation.md#how-does-this-affect-me).

Vamos fazer uma análise aprofundada da migração do modelo de implantação clássico do Azure para o modelo de implantação do Azure Resource Manager. Nós vamos examinar os recursos no nível da funcionalidade e do recurso para ajudá-lo a entender como a plataforma do Azure migra recursos entre os dois modelos de implantação. Para obter mais informações, leia o artigo de comunicado do serviço - [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gateway de VPN clássico para migração do Resource Manager](../../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrar circuitos do ExpressRoute e redes virtuais associadas do clássico para o modelo de implantação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
