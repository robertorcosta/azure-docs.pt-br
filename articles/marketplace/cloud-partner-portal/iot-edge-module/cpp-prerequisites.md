---
title: Pré-requisitos do módulo Azure IoT Edge | Mercado Azure
description: Pré-requisitos para publicar um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9a54b31725d14a3ff54bd2d945cd69f4b8769b87
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983170"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Pré-requisitos de publicação do módulo IoT Edge

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento das ofertas do seu módulo IoT Edge para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://aka.ms/AzureCreateIoT) para gerenciar suas ofertas migradas.

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
