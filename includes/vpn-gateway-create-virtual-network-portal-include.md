---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68780185"
---
Para criar uma VNet no modelo de implantação do Gerenciador de recursos usando o portal do Azure, siga as etapas abaixo. Use os **valores de exemplo** se você estiver usando estas etapas como um tutorial. Se você não estiver seguindo estas etapas como um tutorial, substitua os valores pelos seus próprios. Para saber mais sobre como trabalhar com as redes virtuais, consulte a [Visão Geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Para que essa VNet se conecte a uma localização local, você precisa coordenar com seu administrador de rede local para que o retenha um intervalo de endereços IP que você possa usar especificamente para essa rede virtual. Se existir um intervalo de endereços duplicado em ambos os lados da conexão VPN, o tráfego não encaminhará a forma como você pode esperar. Além disso, se você quiser conectar essa VNet a outra VNet, o espaço de endereço não poderá se sobrepor a outra VNet. Tome cuidado para planejar sua configuração de rede adequadamente.
>

1. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **criar um recurso**. No campo **Pesquisar no Marketplace** , digite "rede virtual". Localize a **rede virtual** na lista retornada e clique para abrir a página **rede virtual** .
3. Clique em **Criar**. Isso abre a página **criar rede virtual** .
4. Na página **criar rede virtual** , defina as configurações de VNet. Quando você preenche os campos, o ponto de exclamação vermelho se torna uma marca de seleção verde quando os caracteres inseridos no campo são válidos. Use os seguintes valores:

   - **Nome**: VNet1
   - **Espaço de endereço**: 10.1.0.0/16
   - **Assinatura**: Verifique se a assinatura listada é aquela que você deseja usar. Você pode alterar as assinaturas usando a lista suspensa.
   - **Grupo de recursos**: TestRG1 (clique em **criar novo** para criar um novo grupo)
   - **Local**: Leste dos EUA
   - **Sub-rede**: frontend
   - **Intervalo de endereços**: 10.1.0.0/24

   ![Página criar rede virtual](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Deixe DDoS como básico, pontos de extremidade de serviço como desabilitado e firewall como desabilitado.
6. Clique em **Criar** para criar a VNet.