---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5bf93980a8be86c77240ab981eb812a738a96204
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828888"
---
É possível criar uma VNet com um modelo de implantação do Resource Manager e com o portal do Azure seguindo estas etapas. Para obter mais informações sobre redes virtuais, confira [Visão geral da rede virtual](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para que essa VNet se conecte a uma localização local, coordene com o administrador de rede local para que ele consiga um intervalo de endereços IP que possa ser usado especificamente para essa rede virtual. Se houver um intervalo de endereços duplicado nos dois lados da conexão de VPN, o tráfego será roteado inesperadamente. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Planeje sua configuração de rede de forma adequada.
>
>

1. Entre no [portal do Azure](https://portal.azure.com).  No menu portal do Azure ou na **Home** Page do e selecione **criar um recurso**. A página **Novo** é aberta.

2. Em **Pesquisar no Marketplace**, insira *rede virtual* e selecione **rede virtual** nos resultados.

   ![Página localizar recurso de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Página localizar recurso de rede virtual")

   A página **Rede virtual** é aberta.

3. Na lista **Selecionar um modelo de implantação** próxima à parte inferior da página, selecione **Resource Manager** e, em seguida, **Criar**. A página **Criar rede virtual** é aberta.

   ![Página criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Criar página da rede virtual")

4. Na página **Criar rede virtual**, defina as configurações da VNet. Quando preencher os campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres inseridos no campo forem validados. Alguns valores são preenchidos automaticamente, que podem ser substituídos por seus próprios valores:

   - **Nome**: insira o nome de sua rede virtual.

   - **Espaço de endereço**: insira o espaço de endereço. Se houver vários espaços de endereço para adicionar, insira o primeiro aqui. É possível adicionar outros espaços de endereço posteriormente, depois de criar a VNet.

   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.

   - **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo inserindo um nome para o novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Local**: selecione o local de sua rede virtual. A localização determina onde ficarão os recursos que você implanta nessa rede virtual.

   - **Sub**-rede: Adicione o **nome** da sub-rede e o **intervalo de endereços**da sub-rede. É possível adicionar outras sub-redes posteriormente, depois de criar a VNet. 
     
5. Clique em **Criar**.
