---
title: Pré-requisitos de máquinas virtuais para o Microsoft Azure | Mercado Azure
description: Lista de pré-requisitos necessários para publicar uma oferta VM no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277609"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual

Este artigo lista os requisitos técnicos e comerciais que você deve atender antes de publicar uma oferta de VM para o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se você ainda não fez isso, revise o [Guia de Publicação de Ofertas de Máquinas Virtuais](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

Pré-requisitos técnicos para a publicação de uma solução de máquina virtual (VM) são simples:

- Você deve ter uma conta ativa do Azure. Se você não tiver uma, inscreva-se no [site do Microsoft Azure](https://azure.microsoft.com).  
- Você deve ter um ambiente configurado para dar suporte ao desenvolvimento da Vm do Windows ou Linux.  Para obter mais informações, consulte o site de documentação de VM associado:
    - [Documentação de VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação de VMs Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos de negócios

Os requisitos de negócios incluem obrigações contratuais, legais e de procedimentos: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Você deve ser um Editor de Marketplace de Nuvem registrado.  Se você não estiver registrado ainda, siga as etapas no artigo [Tornar-se um Editor do Marketplace de Nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com).
    > Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.
    
- Sua empresa (ou sua subsidiária) deve estar localizada em uma empresa de venda/região apoiada pelo Azure Marketplace.  Para obter uma lista atual desses países/regiões, consulte Políticas de [Participação no Mercado do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace.  Para obter mais informações, consulte [opções de faturamento no Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de comunidade.
- Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
- Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no portal do Azure. <!-- TD: Meaning/links? -->
- Você deve concordar com os termos das Políticas de Participação do [Mercado microsoft azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e do Contrato de Editor.
- Você deve estar em conformidade com os [Termos de Uso do Site do Microsoft Azure,](https://azure.microsoft.com/support/legal/website-terms-of-use/)a [Declaração](https://privacy.microsoft.com/privacystatement)de Privacidade da Microsoft e [o Contrato do Programa Certificado do Microsoft Azure.](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)


## <a name="next-steps"></a>Próximas etapas

Depois de ter cumprido esses pré-requisitos, você pode [criar sua oferta de VM](./cpp-create-offer.md).
