---
title: Oferta de máquina virtual no Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de VM no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142794"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar uma máquina virtual do Azure oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) para gerenciar suas ofertas migradas.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Esta seção explica como publicar uma nova oferta de máquina virtual no [Azure Marketplace](https://azuremarketplace.microsoft.com). O suporte é fornecido para máquinas virtuais baseadas em Windows e em Linux, que contiverem um disco rígido virtual do sistema operacional (VHD) e zero ou mais VHDs de dados. | ![Ícone de máquina virtual](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Visão geral da publicação

O vídeo a seguir, [Otimize a sua oferta do Azure Marketplace](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), apresenta uma visão geral ampla do Azure Marketplace, incluindo como publicar neste marketplace (usando uma solução de máquina virtual), como otimizar a experiência do usuário com sua página de produto, a experiência de Test Drive opcional, como gerar leads de usuário e como consumi-los e otimizar o envolvimento do cliente.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Fluxo do processo de publicação

O diagrama a seguir ilustra as etapas de alto nível na publicação de uma oferta de VM. 

![Processo de publicação de VM](./media/publishvm_001.png)

1. Criar a oferta - todos os detalhes e informações sobre a oferta estão configurados, incluindo a descrição da oferta, informações dos materiais, jurídicas e de suporte e especificações do ativo.

2. Criar ativos técnicos e negócios - criar ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associado (aqui, as VMs e discos anexados). 

3. Criar a SKU – criar as SKUs associadas à oferta e enviá-las.  Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar. 
 
4. Certificar e publicar a oferta - depois de concluir a oferta e os recursos técnicos, envie-a. Isso iniciará o processo de publicação, no qual o modelo de solução é testado, validado, certificado e, em seguida vai “ao vivo” no marketplace.  

## <a name="next-steps"></a>Próximas etapas

Antes de considerar essas etapas, cumpra os [requisitos técnicos e comerciais](./cpp-prerequisites.md) para a publicação de uma VM no Microsoft Azure Marketplace. 
