---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a826bb01a906153b4ca2a3c854623c83b6192aec
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828896"
---
Para criar uma Rede Virtual no modelo de implantação do Gerenciador de Recursos usando o portal do Azure, siga as etapas abaixo. As capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Para saber mais sobre como trabalhar com as redes virtuais, consulte a [Visão Geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Se quiser que essa rede virtual conecte-se a uma localização local (além de criar uma configuração P2S), é necessário coordenar com o administrador de rede local para conseguir um intervalo de endereços IP que você pode ser usado especificamente para essa rede virtual. Se um intervalo de endereços duplicado existir em ambos os lados da conexão de VPN, o tráfego não será roteado da maneira esperada. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Tome cuidado e planeje sua configuração de rede de forma adequada.
>
>

1. Entre no [portal do Azure](https://portal.azure.com).  No menu portal do Azure ou na **Home** Page do e selecione **criar um recurso**. A página **Novo** é aberta.

2. Em **Pesquisar no Marketplace**, insira *rede virtual* e selecione **rede virtual** nos resultados.

   ![Página localizar recurso de rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Página localizar recurso de rede virtual")

3. Perto da parte inferior da página Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** e clique em **Criar**.

   ![Selecionar Gerenciador de recursos](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Selecione Gerenciador de Recursos")
4. Na página **Criar rede virtual**, defina as configurações da VNet. Durante o preenchimento dos campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres digitados no campo forem válidos. Talvez alguns valores sejam preenchidos automaticamente. Se forem, substitua os valores pelos seus próprios. A página **Criar rede virtual** é semelhante ao exemplo a seguir:

   ![Validação de campo](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Validação do campo")
5. **Nome**: insira o nome de sua Rede Virtual.
6. **Espaço de endereço**: insira o espaço de endereço. Se houver vários espaços de endereço para adicionar, adicione seu espaço de endereço primeiro. Você pode adicionar outros espaços de endereço posteriormente, depois de criar a rede virtual.
7. **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.
8. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
9. **Local**: selecione o local de sua rede virtual. O local determina onde ficarão os recursos que você implanta nessa rede virtual.
10. **Sub-rede**: adicione o nome da sub-rede e o intervalo de endereços de sub-rede. Você pode adicionar outras sub-redes posteriormente, depois de criar a rede virtual.
11. Selecione **Fixar no painel** se quiser ser capaz de encontrar sua VNet facilmente no painel, em seguida, clique em **Criar**.

    ![Fixar no painel](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "Fixar no painel")
12. Depois de clicar em **Criar**, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.

    ![Bloco criando rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Criar bloco de rede virtual")
