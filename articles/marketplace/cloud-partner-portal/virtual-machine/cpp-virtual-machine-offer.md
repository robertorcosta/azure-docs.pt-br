---
title: Oferta de máquina virtual no Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de VM no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: 939a5f6a4c70a8a1229507e0357cb588c17152fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288760"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

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
