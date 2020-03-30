---
title: Configure os Serviços de Análise do Azure para fontes de dados VNet | Microsoft Docs
description: Saiba como configurar um servidor azure Analysis Services para usar um gateway para fontes de dados na Rede Virtual Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572271"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usar gateway para fontes de dados em uma VNet (Rede Virtual) do Azure

Este artigo descreve a propriedade de servidor Azure Analysis Services **AlwaysUseGateway** para uso quando as fontes de dados estão em uma [Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso ao servidor para fontes de dados VNet

Se as fontes de dados forem acessadas por meio de uma VNet, o servidor do Azure Analysis Services deverá conectar essas fontes de dados como se estivessem no local, no próprio ambiente. Você pode configurar a propriedade do servidor **AlwaysUseGateway** para especificar o servidor para acessar todas as fontes de dados através de um [gateway on-premises](analysis-services-gateway.md). 

As fontes de dados gerenciadas do Banco de Dados Do Azure SQL são executadas no Azure VNet com um endereço IP privado. Se o ponto final público estiver ativado na instância, um gateway não será necessário. Se o ponto final público não estiver ativado, um Gateway de Dados on-premises é necessário e a propriedade AlwaysUseGateway deve ser definida como true.

> [!NOTE]
> Essa propriedade só é eficaz quando um [Gateway de Dados On-premises](analysis-services-gateway.md) é instalado e configurado. O gateway pode estar na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. No SSMS > servidor > **Properties** > **General,** selecione **Mostrar propriedades avançadas (todas).**
2. Em **ASPaaS\AlwaysUseGateway**, selecione **true**.

    ![Sempre use propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Confira também
[Conectando-se a fontes de dados locais](analysis-services-gateway.md)   
[Instale e configure um gateway de dados no local](analysis-services-gateway-install.md)   
[Rede Virtual Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

