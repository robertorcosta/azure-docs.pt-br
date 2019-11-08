---
title: Pré-requisitos de máquina virtual para Microsoft Azure | Azure Marketplace
description: Lista de pré-requisitos necessários para publicar uma oferta VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824431"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual

Este artigo lista os requisitos técnicos e de negócios que você deve atender antes de publicar uma oferta de VM no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se você ainda não tiver feito isso, examine o [Guia de publicação da oferta da máquina virtual](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

Pré-requisitos técnicos para a publicação de uma solução de máquina virtual (VM) são simples:

- Você deve ter uma conta ativa do Azure. Se você não tiver uma, inscreva-se no [site do Microsoft Azure](https://azure.microsoft.com).  
- Você deve ter um ambiente configurado para dar suporte ao desenvolvimento da Vm do Windows ou Linux.  Para obter mais informações, consulte o site de documentação de VM associado:
    - [Documentação de VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação de VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos de negócios

Os requisitos de negócios incluem obrigações contratuais, legais e de procedimentos: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Você deve ser um Editor de Marketplace de Nuvem registrado.  Se você não estiver registrado ainda, siga as etapas no artigo [Tornar-se um Editor do Marketplace de Nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com).
    > Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.
    
- Sua empresa (ou sua subsidiária) deve estar localizada em uma região de venda/país com suporte do Azure Marketplace.  Para obter uma lista atual desses países/regiões, consulte [Microsoft Azure Marketplace políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace.  Para obter mais informações, consulte [Opções de cobrança no Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de comunidade.
- Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
- Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no portal do Azure. <!-- TD: Meaning/links? -->
- Você deve concordar com os termos do [Políticas de Participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e Contrato de Editor.
- Você deve obedecer à [Microsoft Azure termos de uso do site](https://azure.microsoft.com/support/legal/website-terms-of-use/), [política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)e [contrato de programa certificado Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Próximas etapas

Depois de atender a esses pré-requisitos, você poderá [criar sua oferta de VM](./cpp-create-offer.md).
