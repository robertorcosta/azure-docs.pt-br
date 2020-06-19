---
title: Configurar funções do Azure Analysis Services para fonte de dados de VNet | Microsoft Docs
description: Saiba como configurar um servidor de Azure Analysis Services para usar um gateway para fontes de dados na VNet (Rede Virtual) do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7622b6b5855850a0150f43863fa117828e23d4bd
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698877"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usar gateway para fontes de dados em uma VNet (Rede Virtual) do Azure

Este artigo descreve a propriedade da propriedade do servidor **AlwaysUseGateway** do Azure Analysis Services para uso quando as fontes de dados estiverem em uma [VNet (Rede Virtual) do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso ao servidor para fontes de dados VNet

Se as fontes de dados forem acessadas por meio de uma VNet, o servidor do Azure Analysis Services deverá conectar essas fontes de dados como se estivessem no local, no próprio ambiente. É possível configurar a propriedade do servidor **AlwaysUseGateway** para especificar o servidor a acessar todas as fontes de dados por meio de um [Gateway local](analysis-services-gateway.md). 

As fontes de dados da Instância Gerenciada do Banco de Dados SQL do Azure são executadas na VNet do Azure com um endereço IP privado. Se o ponto de extremidade público estiver habilitado na instância, não será necessário um gateway. Se o ponto de extremidade público não estiver habilitado, será necessário um gateway de dados local e a propriedade AlwaysUseGateway deverá ser definida como True.

> [!NOTE]
> Esta propriedade será efetiva somente quando um [Gateway de dados local](analysis-services-gateway.md) estiver instalado e configurado. O gateway pode estar na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. Em SSMS > servidor > **Propriedades** > **Geral**, selecione **Mostrar Propriedades Avançadas (Todas)** .
2. Em **ASPaaS\AlwaysUseGateway**, selecione **true**.

    ![Sempre use propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Confira também
[Conectando fontes de dados locais](analysis-services-gateway.md)   
[Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md)   
[VNET (Rede Virtual) do Azure](../virtual-network/virtual-networks-overview.md)   

