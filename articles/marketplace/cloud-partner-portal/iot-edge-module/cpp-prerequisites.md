---
title: Pré-requisitos do módulo Azure IoT Edge | Mercado Azure
description: Pré-requisitos para publicar um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286533"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Pré-requisitos de publicação do módulo IoT Edge

Este artigo descreve os pré-requisitos para publicar uma oferta de módulo do IoT Edge.  Se você ainda não fez isso, revise o [guia de publicação dos módulos IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Pré-requisitos de publicação

Para publicar um módulo IoT Edge no Azure Marketplace, você precisa atender aos seguintes pré-requisitos:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Acesso a [Portal do Cloud Partner](https://cloudpartner.azure.com/). Para obter mais informações, confira [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Acordo com os [Termos do Marketplace do Azure](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hospede seu ativo técnico do módulo IoT Edge em um Azure Container Registry.  Para obter mais informações, consulte [como preparar seu IoT Edge recursos técnicos do módulo](./cpp-create-technical-assets.md)
- Ter os metadados do módulo do IoT Edge prontos para uso. Por exemplo, prepare os seguintes ativos:
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40 x 40 px, 90 x 90 px, 115 x 115 px, 255 x 115 px)
    - Um termo de uso e uma política de privacidade
    - Uma configuração de módulo padrão que inclui: rotas, propriedades desejadas gêmeas, createOptions e variáveis de ambiente.
    - Documentação do módulo
    - Contatos de suporte


## <a name="next-steps"></a>Próximas etapas

Depois de [ter preparado seu ativo técnico do módulo IoT Edge,](./cpp-create-technical-assets.md)você estará pronto para criar sua oferta de módulo [IoT Edge](./cpp-create-offer.md). 
