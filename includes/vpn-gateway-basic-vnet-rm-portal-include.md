---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619666"
---
É possível criar uma VNet com um modelo de implantação do Resource Manager e com o portal do Azure seguindo estas etapas. Para obter mais informações sobre redes virtuais, confira [Visão geral da rede virtual](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para que essa VNet se conecte a uma localização local, coordene com o administrador de rede local para que ele consiga um intervalo de endereços IP que possa ser usado especificamente para essa rede virtual. Se houver um intervalo de endereços duplicado nos dois lados da conexão de VPN, o tráfego será roteado inesperadamente. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Planeje sua configuração de rede de forma adequada.
>
>

1. Entre no [portal do Azure](https://portal.azure.com).
1. Em **Pesquisar recursos, serviço e documentos (G +/)** , digite *rede virtual*.

   ![Página localizar recurso de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Página localizar recurso de rede virtual")
1. Selecione **rede virtual** nos resultados do **Marketplace** .

   ![Selecionar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Página localizar recurso de rede virtual")
1. Na página **rede virtual** , clique em **criar**.

   ![página rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Clique em Criar.")
1. Depois de clicar em criar, a página **criar rede virtual** é aberta.

   ![Página criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Criar página da rede virtual")
1. Na página **Criar rede virtual**, defina as configurações da VNet. Quando preencher os campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres inseridos no campo forem validados. Alguns valores são preenchidos automaticamente, que podem ser substituídos por seus próprios valores:

   - **Nome**: insira o nome de sua rede virtual.
   - **Espaço de endereço**: insira o espaço de endereço. Se houver vários espaços de endereço para adicionar, insira o primeiro aqui. É possível adicionar outros espaços de endereço posteriormente, depois de criar a VNet. Se sua configuração exigir espaço de endereço IPv6, marque a caixa de seleção para inserir essas informações.
   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.
   - **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo inserindo um nome para o novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Local**: selecione o local de sua rede virtual. A localização determina onde ficarão os recursos que você implanta nessa rede virtual.
   - **Sub**-rede: Adicione o **nome** da sub-rede e o **intervalo de endereços**da sub-rede. É possível adicionar outras sub-redes posteriormente, depois de criar a VNet.
   - **Proteção contra DDoS**: selecione **básica**, a menos que você queira usar o serviço padrão.
   - **Pontos de extremidade de serviço**: você pode deixar essa configuração como **desabilitada**, a menos que sua configuração especifique essa configuração.
   - **Firewall**: você pode deixar essa configuração como **desabilitada**, a menos que sua configuração especifique essa configuração.
1. Clique em **criar** para iniciar a implantação de rede virtual.
