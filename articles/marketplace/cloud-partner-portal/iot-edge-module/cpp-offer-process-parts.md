---
title: Visão geral de publicação de oferta de módulo de Azure IoT Edge | Azure Marketplace
description: Visão geral do processo de publicação de um módulo IoT Edge da oferta no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e9116e5cdb3bd9ed61205ceabd4d51c96c6aadc2
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144661"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Visão geral da publicação da oferta de Módulo do IoT Edge

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de módulo IoT Edge para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar um módulo de IOT Edge oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) para gerenciar suas ofertas migradas.

<table> <tr> <td>Esta seção explica como publicar uma nova oferta de módulo do Azure IoT Edge para o <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> da Microsoft. Um módulo IoT Edge é um contêiner compatível com Docker que é feito para ser executado em um dispositivo IoT Edge. Módulos do IoT Edge do Azure são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure ou o código de solução personalizada. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Processo de publicação

As etapas de alto nível para publicar uma oferta de Módulo do IoT Edge são:

1. Criar a oferta<br> Forneça informações detalhadas sobre a oferta. Essas informações incluem: descrição da oferta, especificações de marketing, informações de suporte e especificações de ativo.

2. Criar ativos comerciais e técnicos<br> Crie ativos comerciais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (as imagens de módulo do IoT Edge hospedadas em um Registro de Contêiner do Azure.

3. Criar a SKU<br> Criar as SKU(s) associadas à oferta. Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar.

4. Certificar e publicar a oferta <br>Depois de concluir a oferta e os recursos técnicos, envie-a. Esse envio inicia o processo de publicação. Durante esse processo a solução é testada, validada, certificada, em seguida, "entra no ar" no Azure Marketplace.

## <a name="parts-of-an-offer"></a>Partes de uma oferta

Os artigos a seguir cobrem as principais partes de uma oferta de módulo do Azure IoT Edge.

- [Pré-requisitos](./cpp-prerequisites.md) <br>Este artigo lista as técnicas e os requisitos de negócios antes de poder criar ou publicar uma oferta de módulo do Azure IoT Edge.
- [Preparar ativos técnicos de módulo do Azure IoT Edge](./cpp-create-technical-assets.md) <br>Este artigo descreve como preparar os recursos técnicos para um módulo do Azure IoT Edge. Esses ativos devem atender a todos os critérios técnicos necessários antes do módulo do Azure IoT Edge pode ser publicado no Azure Marketplace.
- [Criar uma oferta de módulo do IoT Edge](./cpp-create-offer.md) <br>Este artigo lista as etapas necessárias para criar uma nova oferta do módulo do Azure IoT Edge usando o [Portal do Cloud Partner](https://cloudpartner.azure.com).
- [Publicar oferta de módulo do IoT Edge](./cpp-publish-offer.md)<br> Este artigo descreve como enviar a oferta para publicar no Microsoft Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

Examine os [requisitos técnicos e comerciais](./cpp-prerequisites.md) para a publicação de um módulo do Azure IoT Edge ao Microsoft Azure Marketplace.
