---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3d9826e3eae2a60b217df1406d26d83c78fbdefb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67673231"
---
Para criar uma VNet no modelo de implantação do Gerenciador de recursos usando o portal do Azure, siga as etapas abaixo. As capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Para saber mais sobre como trabalhar com as redes virtuais, consulte a [Visão Geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Se você quiser que essa VNet se conecte a um local (além de criar uma configuração de P2S), será necessário coordenar com o administrador de rede local para que ele crie um intervalo de endereços IP que você possa usar especificamente para essa rede virtual. Se existir um intervalo de endereços duplicado em ambos os lados da conexão VPN, o tráfego não encaminhará a forma como você pode esperar. Além disso, se você quiser conectar essa VNet a outra VNet, o espaço de endereço não poderá se sobrepor a outra VNet. Tome cuidado para planejar sua configuração de rede adequadamente.
>
>

1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **+** . No campo **Pesquisar no Marketplace** , digite "rede virtual". Localize a **rede virtual** na lista retornada e clique para abrir a página **rede virtual** .

   ![Página localizar recurso de rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/newvnetportal700.png "Página localizar recurso de rede virtual")
3. Próximo à parte inferior da página rede virtual, na lista **selecionar um modelo de implantação** , selecione **Gerenciador de recursos**e clique em **criar**.

   ![Selecionar Gerenciador de recursos](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Selecionar Gerenciador de recursos")
4. Na página **criar rede virtual** , defina as configurações de VNet. Quando você preenche os campos, o ponto de exclamação vermelho se torna uma marca de seleção verde quando os caracteres inseridos no campo são válidos. Pode haver valores preenchidos automaticamente. Nesse caso, substitua os valores pelos seus próprios. A página **criar rede virtual** é semelhante ao exemplo a seguir:

   ![Validação de campo](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Validação de campo")
5. **Nome**: insira o nome de sua Rede Virtual.
6. **Espaço de endereço**: insira o espaço de endereço. Se você tiver vários espaços de endereço para adicionar, adicione seu primeiro espaço de endereço. Você pode adicionar outros espaços de endereço posteriormente, depois de criar a VNet.
7. **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando a lista suspensa.
8. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, nomeie o grupo de recursos de acordo com os valores de configuração planejados. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
9. **Local**: selecione o local de sua rede virtual. O local determina onde residem os recursos que serão implantados nesta VNet.
10. **Sub**-rede: Adicione o nome da sub-rede e o intervalo de endereços da sub-rede. Você pode adicionar mais sub-redes posteriormente, depois de criar a VNet.
11. Selecione **Fixar no painel** se quiser encontrar sua VNet facilmente no painel. Em seguida, clique em **Criar**.

    ![Fixar no painel](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "Fixar no painel")
12. Depois de clicar em **Criar**, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.

    ![Bloco criando rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Bloco criando rede virtual")
