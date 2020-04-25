---
title: Pré-requisitos do módulo de Azure IoT Edge | Azure Marketplace
description: Pré-requisitos para publicar um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142380"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Pré-requisitos de publicação do módulo IoT Edge

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de módulo IoT Edge para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar um módulo de IOT Edge oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) para gerenciar suas ofertas migradas.

Este artigo descreve os pré-requisitos para publicar uma oferta de módulo do IoT Edge.  Se você ainda não tiver feito isso, examine o [Guia de publicação de módulos de IOT Edge](../..//iot-edge-module.md).


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

Depois de [preparar seu ativo técnico de IOT Edge módulo](./cpp-create-technical-assets.md), você estará pronto para [criar sua oferta de módulo IOT Edge](./cpp-create-offer.md). 
