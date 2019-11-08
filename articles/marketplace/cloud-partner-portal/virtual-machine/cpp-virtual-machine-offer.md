---
title: Oferta de máquina virtual no Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 0313c4f2581615e1d1a3a57fc191db7d7cc6e825
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808568"
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
